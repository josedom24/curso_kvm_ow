# Uso de un pool de almacenamiento de tipo disk

En este apartado, aprenderemos a trabajar con un pool de almacenamiento de tipo **disk**.

* Un pool de tipo **disk** en libvirt permite gestionar un disco físico como un recurso de almacenamiento, facilitando la creación y administración de volúmenes para máquinas virtuales.
* En un pool de tipo **disk**, un volumen es una partición del disco físico, que puede usarse como disco virtual para las máquinas virtuales.

 Supondremos que tenemos un disco de 20GB sin formatear conectado a nuestro host que corresponde al dispositivo de bloque `/dev/vdb`.

## Creación del pool de almacenamiento

Primero, debemos identificar el disco que usaremos. Ejecutamos:
```
usuario@kvm:~$ lsblk
...
vdb    253:16   0    20G  0 disk
```

Tenemos que preparar el disco creando una tabla de particiones:

```
usuario@kvm:~$ sudo parted /dev/vdb -- mklabel gpt
```

Creamos el pool de almacenamiento llamado `pool-disk` con el siguiente comando:

```
usuario@kvm:~$ virsh pool-define-as pool-disk disk --source-dev /dev/vdb --source-format gpt --target /dev
```
* El parámetro `--device-dev` especifica la ruta del dispositivo de almacenamiento. 
* El parámetro `--target` es el directorio donde se crean los dispositivos de bloque correspondientes a las particiones.
* El parámetro `--source-format` especifica el tipo de tabla de partición.


Y ya podemos iniciar y configurar el pool para que se active automáticamente al inicio:
```
usuario@kvm:~$ virsh pool-start pool-disk
usuario@kvm:~$ virsh pool-autostart pool-disk
```

Verificamos que el pool esté activo:
```
usuario@kvm:~$ virsh pool-list --all
```

## Creación de volúmenes

Como indicábamos los volúmenes en un pool de tipo **disk** serán particiones del disco. Podemos crear estas particiones usando la API de libvirt, por ejemplo con `virsh` o con herramientas específicas.

### Crear un volumen virsh

Crearemos un volumen de 10GB dentro del pool `pool-disk`:
```
usuario@kvm:~$ virsh vol-create-as pool-disk vdb1 10G
```

Verificamos que el volumen se haya creado correctamente:

```
usuario@kvm:~$ virsh vol-list pool-disk
```

### Crear un volumen con herramientas específicas

Otra estrategia es crear la partición en el dispositivo de bloque y posteriormente refrescamos el pool de almacenamiento. Usamos `parted` para crear una partición de 5GB:

```
usuario@kvm:~$ sudo parted /dev/vdb print
...
Número  Inicio  Fin     Tamaño  Sistema de archivos  Nombre   Banderas
 1      17,4kB  10,7GB  10,7GB                       primary

usuario@kvm:~$ sudo parted /dev/vdb mkpart primary ext4 10700MiB 15700MiB
```

Refrescamos el pool en libvirt para reconocer la nueva partición:
```
usuario@kvm:~$ virsh pool-refresh pool-disk
```

Verificamos que el nuevo volumen aparezca:
```
usuario@kvm:~$ virsh vol-list pool-disk 
 Name   Path
-------------------
 vdb1   /dev/vdb1
 vdb2   /dev/vdb2
```

Y comprobamos el espacio que queda en el pool de almacenamiento:

```
usuario@kvm:~$ virsh pool-info pool-disk 
Name:           pool-disk
UUID:           5dd18d82-1e29-4c01-a1c2-260b3e0d591a
State:          running
Persistent:     yes
Autostart:      no
Capacity:       20,00 GiB
Allocation:     14,88 GiB
```

## Crear una máquina virtual con los volúmenes que hemos creado

Usaremos `virt-install` para crear una máquina virtual utilizando los volúmenes creados.

```
usuario@kvm:~$ virt-install --connect qemu:///system \
                            --name maquina-disco \
                            --cdrom /var/lib/libvirt/images/debian-12.10.0-amd64-netinst.iso \
                            --os-variant debian12 \
                            --disk vol=pool-disk/vdb1 \
                            --disk path=/dev/vdb2,device=disk \
                            --memory 2048 \
                            --vcpus 2 
```

Donde hemos añadido los dos volúmenes que hemos creado:

- `--disk vol=pool-disk/vol1`: Usa el primer volumen de 10GB, indicamos el nombre del pool y el nombre del volumen.
- `--disk path=/dev/vdb2,device=disk`: En este caso referenciamos al volumen indicado la partición directamente.


