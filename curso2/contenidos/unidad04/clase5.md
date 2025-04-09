# Trabajar con volúmenes en las máquinas virtuales

## Creación de máquinas virtuales usando volúmenes existentes

En apartados anterior creamos un volumen de 10 GB llamado `nuevodisco.img`. Vamos a crear una nueva máquina virtual que tenga como disco duro este volumen, para ello ejecutamos la siguiente instrucción: 

```
usuario@kvm:~$ virt-install --connect qemu:///system \
                            --virt-type kvm \
                            --name otro_debian12 \
                            --cdrom /var/lib/libvirt/images/debian-12.10.0-amd64-netinst.iso \
                            --os-variant debian12 \
                            --disk vol=vm-images/nuevodisco.img \
                            --memory 2048 \
                            --vcpus 2
```			 

Tenemos varias opciones para indicar el volumen:

* `--disk vol=vm-images/nuevodisco.img`, es la opción que hemos usado, con ella indicamos el volumen usando el formato `pool/volumen`. 
* `--disk path=/srv/images/nuevodisco.img`: Donde indicamos directamente la ruta donde se encuentra el fichero de imagen de disco. Es importante entender, que no es necesario que el fichero de imagen de disco que utilicemos este guardado en un pool, simplemente ha de ser accesible por libvirt. Los pool de almacenamiento, simplemente nos ofrecen un mecanismo para gestionar de forma más eficiente los volúmenes de almacenamiento.
* `--pool vm-images,size=10`: En este caso no se reutiliza el volumen que tenemos creado, sino que se crearía un nuevo volumen de 10GB en el pool indicado.

Indicar que podríamos añadir más parámetros `--disk` para añadir más discos a la máquina virtual.

## Añadir nuevos discos a máquinas virtuales

Para añadir un disco a una máquina virtual, vamos a modificar su definición XML. Podríamos usar `virsh edit` e incluir la definición XML del nuevo disco. Sin embargo, vamos a usar un comando de `virsh` que nos facilita la operación de añadir un nuevo disco y por tanto, la modificación de la definición XML de la máquina. Hay que indicar que esta modificación se puede hacer "en caliente", con la máquina funcionando.

Por lo tanto, vamos añadir el volumen `disco1.qcow2` que creamos en el apartado anterior, a nuestra máquina Linux, ejecutamos:

```
usuario@kvm:~$ virsh attach-disk debian12 /srv/images/disco1.qcow2 vdb --driver=qemu --type disk --subdriver qcow2 --persistent
```

Donde indicamos los siguientes parámetros:

* El nombre de la máquina.
* El path del fichero de imagen.
* El dispositivo de bloque que se va a crear.
* El driver con el parámetro `--driver=qemu`.
* El tipo que será un disco, con `--type disk`.
* El formato de la imagen que se va a añadir, con `--subdriver qcow2`.
* Por último, con la opción `--persistent` hacemos el cambio de forma persistente, para que en el próximo reinicio de la máquina se vuelva a añadir el disco.

Si accedemos a la máquina podemos comprobar que efectivamente se ha añadido el disco:

```
usuario@debian:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
...
vdb    254:16   0    1G  0 disk 
```
Y a continuación lo podríamos particionar o formatear con algún sistema de ficheros, por ejemplo:

```
usuario@debian:~$ sudo mkfs.ext4 /dev/vdb
...
```

Y ya podemos montarlo en el sistema de ficheros:

```
usuario@debian:~$ sudo mount /dev/vdb /mnt
```

## Desconectar discos a máquinas virtuales

Para desconectar un disco de una máquina virtual podemos ejecutar:

```
usuario@kvm:~$ virsh detach-disk debian12 vdb --persistent
```

Indicando la máquina virtual, el dispositivo que se había creado y la opción para que sea un cambio persistente.

