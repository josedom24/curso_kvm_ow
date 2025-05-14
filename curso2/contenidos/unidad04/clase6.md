# Redimensión de discos en máquinas virtuales

En este apartado vamos a aumentar el tamaño del volumen que hemos añadido a la máquina Linux, por lo que la máquina verá un disco más grande, pero hay que recordar que también tendremos que redimensionar el sistema de ficheros.

Para realizar la redimensión tenemos dos alternativas: o usar la API de libvirt usando `virsh` o usar herramientas específicas, en este caso `qemu-img`.

## Redimensión de discos en máquinas virtuales paradas

Para redimensionar el volumen de una máquina que esté parada, podemos usar `virsh`:

```
usuario@kvm:~$ virsh vol-resize disco1.qcow2 3G --pool vm-images
```

O podemos usar `qemu-img`, se ejecuta con un usuario con privilegios o con `sudo`:

```
usuario@kvm:~$ sudo qemu-img resize /srv/images/disco1.qcow2 3G
```

## Redimensión de discos en máquinas virtuales en ejecución

Para hacer la redimensión "en caliente", con la máquina encendida, podemos obtener información de los discos conectados a una máquina:

```
usuario@kvm:~$ virsh domblklist debian12
 Target   Source
--------------------------------------------------
 vda      /var/lib/libvirt/images/debian12.qcow2
 vdb      /srv/images/disco1.qcow2
```

Y a continuación, redimensionamos el disco deseado:

```
usuario@kvm:~$ virsh blockresize debian12 /srv/images/disco1.qcow2 3G
```

Podemos comprobar que se ha producido la redimensión en el disco de la máquina. Accedemos a la máquina y hacemos la comprobación:

```
usuario@debian:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
...
vdb    254:16   0    3G  0 disk 
```

El disco ahora tiene 3GB, pero el sistema de archivo sigue teniendo 1Gb:

```
usuario@debian:~$ df -h
S.ficheros     Tamaño Usados  Disp Uso% Montado en
...
/dev/vdb         974M    24K  907M   1% /mnt
```

Desmontamos el disco (es necesario porque está formateado con `ext4`, con otros sistemas de ficheros no sería necesario el desmontaje), y lo redimensionamos:

```
usuario@debian:~$ sudo umount /mnt
usuario@debian:~$ sudo e2fsck -f /dev/vdb
usuario@debian:~$ sudo resize2fs /dev/vdb
usuario@debian:~$ sudo mount /dev/vdb /mnt
usuario@debian:~$ df -h
S.ficheros     Tamaño Usados  Disp Uso% Montado en
...
/dev/vdb         3,0G    24K  2,8G   1% /mnt
```

## Redimensión del sistema de ficheros de una imagen de disco

Otra alternativa para redimensionar el sistema de ficheros de una imagen es usar la herramienta [virt-resize](https://libguestfs.org/virt-resize.1.html). `virt-resize` no trabaja sobre imágenes de discos de máquinas que se estén ejecutando, además no puede redimensionar sobre el mismo fichero de la imagen, por lo que vamos a hacer una copia del mismo. Para utilizar esta herramienta vamos a instalar el siguiente paquete:

```
usuario@kvm:~$ sudo apt install guestfs-tools
```

Lo primero es para la máquina virtual:

```
usuario@kvm:~$ virsh shutdown debian12
```

Vamos a redimensionar el disco raíz de la máquina Linux que tiene 10 Gb de tamaño virtual. Antes de usar `virt-resize`, debemos crear un disco destino donde se va a guardar el disco redimensionado:
```
usuario@kvm:~$ sudo qemu-img create -f qcow2 /var/lib/libvirt/images/nuevo_disco.qcow2 15G
```
Y posteriormente vamos a redimensionar el sistema de archivo de la partición `/dev/sda1` con `virt-resize` y el parámetro `--expand`. Se creará un nuevo volumen que posteriormente sustituiremos por el original:

```
usuario@kvm:~$ sudo virt-resize --expand /dev/sda1 /var/lib/libvirt/images/debian12.qcow2 /var/lib/libvirt/images/nuevo_disco.qcow2
usuario@kvm:~$ sudo mv /var/lib/libvirt/images/nuevo_disco.qcow2 /var/lib/libvirt/images/debian12.qcow2 
```

Iniciamos de nuevo la máquina y comprobamos si el disco y el sistema de archivos se ha redimensionado.