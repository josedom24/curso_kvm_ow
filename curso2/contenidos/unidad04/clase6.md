# Uso de un pool de almacenamiento de tipo disk

En este apartado, aprenderemos a trabajar con un pool de almacenamiento de tipo `disk`.

* Un pool de tipo `disk` en libvirt permite gestionar un disco físico como un recurso de almacenamiento, facilitando la creación y administración de volúmenes para máquinas virtuales.
* En un pool de tipo `disk`, un volumen es una partición del disco físico, que puede usarse como disco virtual para máquinas virtuales.

 Supondremos que tenemos un disco de 20GB sin formatear conectado a nuestro host que es accesible con el dispositivo de bloque `/dev/vdb`.

## Creación del pool de almacenamiento

Primero, debemos identificar el disco que usaremos. Ejecutamos:
```
usuario@ubuntu:~$ lsblk
...
vdb    253:16   0    20G  0 disk
```
Supongamos que el disco aparece como `/dev/vdb`. Lo primero es preparar el disco creando una tabla de particiones:

```
usuario@ubuntu:~$ sudo parted /dev/vdb -- mklabel gpt
```

Creamos el pool de almacenamiento llamado `pool-disk` con el siguiente comando:
```
usuario@ubuntu:~$ virsh pool-define-as pool-disk disk --source-dev /dev/vdb --source-format gpt --target /dev
```
* El parámetro `--device-dev` especifica la ruta del dispositivo de almacenamiento. 
* El parámetro `--target` es el directorio donde se crean los dispositivos de bloque correspondientes a las particiones.
* El parámetro `--source-format` especifica el tipo de tabla de partición.


Y ya podemos iniciar y configuramos el pool para que se active automáticamente al inicio:
```
usuario@ubuntu:~$ virsh pool-start pool-disk
usuario@ubuntu:~$ virsh pool-autostart pool-disk
```

Verificamos que el pool esté activo:
```
usuario@ubuntu:~$ virsh pool-list --all
```

## Creación de volúmenes

Como indicados los volúmenes en un pool de tipo `disk` serán particiones del disco. Podemos crear estas particiones usando la API de libvirt, por ejemplo con `virsh` o con herramientas específicas.

### Crear un volumen virsh

Crearemos un volumen de 10GB dentro del pool `pool-disk`:
```
usuario@ubuntu:~$ virsh vol-create-as pool-disk vdb1 10G
```

Verificamos que el volumen se haya creado correctamente:

```
usuario@ubuntu:~$ virsh vol-list pool-disk
```

### Crear un volumen con herramientas

Otra estrategia es crear la partición en el dispositivo de bloque y posteriormente refrescamos el pool de almacenamiento.
Ahora crearemos una partición en el disco manualmente y luego actualizaremos el pool.

1. Usamos `fdisk` para crear la partición:
   ```
   usuario@ubuntu:~$ sudo fdisk /dev/vdb
   ```
   * Presionamos `n` para crear una nueva partición.
   * Seleccionamos `p` para una partición primaria.
   * Elegimos la partición `2`.
   * Eleigmos el bloque de inicio, dejamos el valor por defecto.
   * Definimos el tamaño (por ejemplo, `+8G` para 8GB).
   * Guardamos los cambios con `w`.

2. Refrescamos el pool en Libvirt para reconocer la nueva partición:
   ```
   usuario@ubuntu:~$ virsh pool-refresh pool-disk
   ```

3. Verificamos que el nuevo volumen aparezca:
   ```sh
   usuario@ubuntu:~$ virsh vol-list pool-disk
   ```

## Crear una máquina virtual con los volúmens

Usaremos `virt-install` para crear una máquina virtual utilizando los volúmenes creados.

```sh
virt-install \
  --name maquina-disco \
  --memory 2048 \
  --vcpus 2 \
  --disk vol=pool-disk/vdb1,format=raw \
  --disk path=/dev/vdb2,device=disk \
  --cdrom /var/lib/libvirt/images/ubuntu.iso \
  --os-type linux \
  --os-variant ubuntu22.04 \
  --network network=default \
  --graphics vnc
```

Explicación de los parámetros:
- `--disk vol=pool-disk/vol1,format=raw`: Usa el primer volumen de 10GB.
- `--disk path=/dev/vdb2,device=disk`: Usa la partición creada manualmente.
- `--cdrom`: Especifica la imagen ISO de instalación.
- `--network network=default`: Usa la red predeterminada de Libvirt.
- `--graphics vnc`: Activa la consola gráfica VNC.

