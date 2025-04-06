# Clonación a partir de plantillas

## Clonación completa a partir de plantillas

La clonación completa a partir de una plantilla es similar a la clonación de máquinas virtuales que vimos en un punto anterior. Podemos usar el siguiente comando para realizar la clonación:

```
usuario@kvm:~$ virt-clone --connect=qemu:///system --original plantilla-debian12 --name nueva-debian12 --auto-clone
```

Recuerda que puedes usar el parámetro `--file` para indicar el nombre de la imagen de la nueva máquina que clonamos. El proceso puede ser lento, ya que se hace una copia completa de la imagen original a la de la nueva máquina virtual.

## Clonación enlazada a partir de plantillas

En este tipo de clonación la imagen de la máquina clonada utiliza la imagen de la plantilla como imagen base (**backing store**) en modo de sólo lectura, en la imagen de la nueva máquina sólo se guardan los cambios del sistema de archivo. Requiere menos espacio en disco, pero no puede ejecutarse sin acceso a la imagen de plantilla base. 

El mecanismo es un poco más complejo, tenemos que realizar dos pasos:

1. Creación del nuevo volumen a a partir de la imagen base de la plantilla (**backing store**).
2. Creación de la nueva máquina usando `virt-install` o `virt-clone`.

### Creación de imágenes de disco con backing store

A continuación vamos a crear un nuevo volumen a partir de la imagen base que hemos creado en el punto anterior. Hay que tener en cuenta lo siguiente:

* El nuevo volumen no puede ser más pequeño que la imagen base.
* El nuevo volumen puede tener más tamaño que la imagen base, pero al iniciar la máquina habrá que redimensionar el tamaño del sistema de ficheros, que será igual al tamaño del sistema de ficheros de la imagen base. Como vimos en un apartado anterior, este cambio de tamaño se podría realizar usando `virt-resize`.

Para no complicar la creación de volúmenes con backing store vamos a indicar el tamaño del nuevo volumen igual al de la imagen base. Como la imagen base ya tiene guardado un sistema de archivos con un tamaño determinado, el hecho de que creemos una nueva imagen con más tamaño no conlleva el redimensionado del sistema de archivo. 

Para asegurarnos de crear un volumen del mismo tamaño que la imagen base vamos comprobar su tamaño:
```
usuario@kvm:~$ virsh domblkinfo plantilla-debian12 vda --human
```

Para crear la nueva imagen basada en la imagen base de la plantilla, podemos crear el volumen con `virsh`:

```
usuario@kvm:~$ virsh vol-create-as default debian12-backing.qcow2 10G --format qcow2 --backing-vol debian12.qcow2 --backing-vol-format qcow2 
```

O podemos usar la aplicación `qemu-img` y posterior refrescamos el pool `default`:

```
usuario@kvm:~$ sudo qemu-img create -f qcow2 -b /var/lib/libvirt/images/debian12.qcow2 -F qcow2 /var/lib/libvirt/images/debian12-backing.qcow2 10G
usuario@kvm:~$ virsh pool-refresh default
```

Para comprobar que un volumen está creado con una imagen base podemos usar `virsh`:

```
usuario@kvm:~$ virsh vol-dumpxml debian12-backing.qcow2 default
...
<backingStore>
    <path>/var/lib/libvirt/images/debian12.qcow2</path>
    <format type='qcow2'/>
    <permissions>
    ...
```

O usando el comando `qemu-img`:

```
sudo qemu-img info /var/lib/libvirt/images/debian12-backing.qcow2
...
backing file: debian12.qcow2
backing file format: qcow2
...
```

### Creación de la nueva máquina a partir de la imagen con backing store con virt-install

En este caso podemos usar la herramienta `virt-install` pero sin indicar el medio de instalación.

```
usuario@kvm:~$ virt-install --connect qemu:///system \
                            --virt-type kvm \
                            --name otra-debian12 \
                            --os-variant debian12 \
                            --disk path=/var/lib/libvirt/images/debian12-backing.qcow2 \
                            --memory 1024 \
                            --vcpus 1 \
                            --import
```		

Usamos la opción `--import` para que no te pida que indique el medio de instalación, simplemente va a usar el volumen indicado como disco de la máquina virtual.


### Creación de la nueva máquina a partir de la imagen con backing store con virt-clone

Una vez que tenemos creado el volumen basada en el imagen base de la plantilla, podemos crear un nuevo clon con `virt-clone`, para ello ejecutamos:

```
usuario@kvm:~$ virt-clone --connect=qemu:///system --original plantilla-debian12 --name otra-debian12 --file /var/lib/libvirt/images/debian12-backing.qcow2 --preserve-data
```

Indicamos como fichero el volumen que hemos creado, pero con la opción `--preserve-data` no se copia el volumen original al nuevo, simplemente se usa. Se puede comprobar que la clonación no tarda nada de tiempo, no se está copiando un volumen en otro.



