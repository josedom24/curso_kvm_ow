# Trabajar con volúmenes en las máquinas virtuales

## Creación de máquinas virtuales usando volúmenes existentes

En apartados anterior creamos un volumen de 10 GB llamado `vol1.qcow2`. Vamos a crear una nueva máquina virtual que tenga como disco duro este volumen, para ello ejecutamos la siguiente instrucción: 

```
usuario@kvm:~$ virt-install --virt-type kvm \
			 --name prueba4 \
			 --cdrom ~/iso/debian-11.3.0-amd64-netinst.iso \
			 --os-variant debian10 \
			 --disk vol=default/vol1.qcow2 \
			 --memory 1024 \
			 --vcpus 1
```			 

Tenemos varias opciones para indicar el volúmen:

* `--disk vol=default/vol1.qcow2`, es la opción que hemos usado, con ella indicamos el volumen usando el formato `pool/volumen`. 
* `--disk path=/var/lib/libvirt/images/vol1.qcow2`: Donde indicamos directamente la ruta donde se encuentra el fichero de imagen de disco.
* `--pool wm-images,size=10`: En este caso no se reutiliza el volumen que tenemos creado, sino que se crearía un nuevo volumen de 10GB en el pool indicado.

## Añadir nuevos discos a máquinas virtuales

Para añadir un disco a una máquina virtual, vamos a modificar su definición XML. Podríamos usar `virsh edit` e incluir la definición XML del nuevo disco. Sin embargo, vamos a usar un comando de `virsh` que nos facilita la operación de añadir un nuevo disco y por tanto, la modificación de la definición XML de la máquina. Hay que indicar que esta modificación se puede hacer "en caliente", con la máquina funcionando.

Por lo tanto, vamos añadir el volumen `vol2.qcow2` que creamos en el apartado anterior, a la máquina que hemos creado en estado de ejecución, ejecutamos:

```
usuario@kvm:~$ virsh attach-disk prueba4 /srv/images/vol2.qcow2 vdb --driver=qemu --type disk --subdriver qcow2 --persistent
El disco ha sido asociado exitosamente
```

Donde indicamos los siguientes parámetros:

* El nombre de la máquina.
* El path del fichero de imagen
* El dispositivo de bloque que se va a crear
* El driver con el parámetro `--driver=qemu`.
* El tipo que será un disco, con `--type disk`.
* El formato de la imagen que se va a añadir, con `--subdriver qcow2`.
* Por último, con la opción `--persistent` hacemos el cambio de forma persistente, para que en el próximo reinicio de la máquina se vuelva a añadir el disco.

Para desconectar un disco de una máquina virtual podemos ejecutar:

```
usuario@kvm:~$ virsh detach-disk prueba4 vdb --persistent
El disco ha sido desmontado exitosamente
```

Indicando la máquina virtual, el dispositivo que se había creado y la opción para que sea un cambio persistente.

## Redimensión de discos en máquinas virtuales

Antes de desconectar el disco de la máquina, vamos a realizar una operación de redimensión. Vamos a aumentar el tamaño del volumen, por lo que la máquina verá un disco más grande, pero hay que recordar que también tendremos que redimensionar el sistemas de ficheros.

Para realizar la redimensión tenemos dos alternativas: o usar la API de libvirt usando `virsh` o usar herramientas especificas, en este caso `qemu-img`.

Para redimensionar el volumen de una máquina que este parada, podemos usar `virsh`:

```
usuario@kvm:~$ virsh vol-resize vol2.qcow2 3G --pool vm-images
El tamaño de volumen 'vol2.qcow2' se ha cambiado correctamente a 3G
```

O podemos usar `qemu-img`, se ejecuta con un usuario con privilegios o con `sudo`:

```
$ qemu-img resize /srv/images/vol2.qcow2 3G
Image resized.
```

Para hacer la redimensión "en caliente", con la máquina encendida, podemos obtener información de los discos conectados a una máquina:

```
usuario@kvm:~$ virsh domblklist prueba4 
 Destino   Fuente
-----------------------------------------------
 vda       /var/lib/libvirt/images/vol1.qcow2
 vdb       /srv/images/vol2.qcow2
```

Y continuación redimensionamos el disco deseado:

```
usuario@kvm:~$ virsh blockresize prueba4 /srv/images/vol2.qcow2 3G
El dispositivo de bloque '/srv/images/vol2.qcow2' cambió de tamaño
```

Podemos comprobar que se ha producido la redimensión en el disco de la máquina:

xxxxxxxxxx

1. El disco ahora tiene 3GB.
2. Pero el sistema de archivo sigue teniendo 2Gb. 
3. Desmontamos el disco, y lo redimensionamos con `resize2fs`.
4. Volvemos a montarlo y comprobamos que ahora ya tiene los 3Gb.

## Redimensión del sistema de ficheros de una imagen de disco

Otra alternativa para redimensionar el sistema de fichero de una imagen es usar la herramienta [virt-resize](https://libguestfs.org/virt-resize.1.html). `virt-resize` no trabaja sobre imágenes de discos de máquinas que se estén ejecutando, además no puede redimensionar sobre el mismo fichero de la imagen, por lo que vamos a hacer una copia del mismo.

Si tenemos un fichero qcow2 que se llama `vol1.qcow`, podemos redimensionar el disco y su sistema de ficheros con al siguientes instrucciones:

```
$ qemu-img resize vol1.qcow2 10G
$ cp vol1.qcow2 newvol1.qcow2
$ virt-resize --expand /dev/sda1 vol1.qcow2 newvol1.qcow2
$ mv newvol1.qcow2 vol1.qcow2
```

Redimensionamos el disco, como vimos en el apartado anterior. Como hemos indicado `virt-resize` no trabaja sobre un fichero qcow2 directamente, es por ello que lo hemos copiado a otro fichero y hemos ejecutado el comando. Finalmente el fichero `nwevol1.qcow2` tendrá un sistema de ficheros de 10Gb, por lo que terminamos copiándolo de nuevo (con `mv`) sobre el disco original.

Alternativa más eficiente:

$ virt-resize --expand /dev/sda1 vol1.qcow2 newvol1.qcow2
$ mv vol1.qcow2 vol1.qcow2.bak  # Guarda un respaldo por si algo sale mal
$ mv newvol1.qcow2 vol1.qcow2