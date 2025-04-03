# Gestión de volúmenes de almacenamiento con herramientas específicas

En este apartado vamos a gestionar los volúmenes con herramientas especificas. Es decir, si estamos trabajando con un pool de tipo **dir** y con volúmenes que corresponde a ficheros de imágenes de disco, vamos a trabajar con la herramienta `qemu-img`. Esta potente herramienta nos permite la gestión completa de los ficheros de imágenes de disco.

## Gestión de imágenes de disco con qemu-img

La herramienta `qemu-img` es una utilidad para gestionar ficheros de imagen de disco. Puedes profundizar en el uso de esta herramienta consultando la documentación oficial: [QEMU disk image utility](https://qemu-project.gitlab.io/qemu/tools/qemu-img.html).

Vamos a crear un nuevo fichero de imagen llamado `disco1.qcow2`, con el formato `qcow2`, con un tamaño de 1GB, en el directorio `/srv/images`, correspondiente al pool `vm-images`, que creamos en un apartado anterior. Vamos ejecutar como `root` las siguientes instrucciones:

```
root@kvm:~# cd /srv/images/
root@kvm:~# qemu-img create -f qcow2 disco1.qcow2 1G
```

Podemos obtener información de la imagen que hemos creado, ejecutando en el mismo directorio:

```
root@kvm:~# qemu-img info disco1.qcow2 
image: disco1.qcow2
file format: qcow2
virtual size: 1 GiB (1073741824 bytes)
disk size: 196 KiB
...
```

La creación del fichero de imagen, no conlleva de forma automática la creación del volumen en el pool de almacenamiento. Si vemos la lista de volúmenes en el pool `vm-images` comprobamos que no se ha creado. Para que se cree un nuevo volumen a partir del fichero que hemos creado, necesitamos **refrescar** el pool, para ello:

```
usuario@kvm:~$ virsh pool-refresh vm-images
```

Y comprobamos que ya tenemos el volumen creado ejecutando: 
```
usuario@kvm:~$ virsh vol-list vm-images`.
```

La herramienta `qemu-img` es muy potente y nos permite realizar muchas operaciones: redimensionar el fichero de imagen, convertir entre formatos de imágenes, crear imágenes a a partir de imágenes base, crear instantáneas de imágenes, ... Utilizaremos algunas de estas funciones en apartados posteriores del curso.

**Nota: Si estuviéramos trabajando con otro tipo de pool de almacenamiento, tendíamos que usar herramientas especificas para gestionar los medios de almacenamientos adecuados. Por ejemplo, si estuviéramos trabajando con un pool de tipo *logical*, usaríamos las herramientas de comando de LVM para crear y gestionar los volúmenes lógicos que se corresponderían con los volúmenes de este tipo de pool.**
