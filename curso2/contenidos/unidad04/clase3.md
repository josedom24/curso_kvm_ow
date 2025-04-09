# Gestión de volúmenes de almacenamiento con virsh

En este apartado vamos a estudiar la gestión de volúmenes de almacenamiento usando la API de libvirt, por lo tanto, utilizaremos la herramienta `virsh`. 

Vamos a trabajar con los pool de almacenamiento que hemos creado que son de tipo **dir**, por lo tanto los volúmenes corresponden a ficheros de imágenes de disco. 

## Obtener información de los volúmenes

Para obtener los volúmenes de un determinado pool (por ejemplo el pool `default`), ejecutamos:

```
usuario@kvm:~$ $ virsh vol-list default
 Name                               Path
----------------------------------------------------------------------------------------------
 debian12.qcow2                     /var/lib/libvirt/images/debian12.qcow2
...
```

Podemos comprobar que los volúmenes listados se corresponden con ficheros que se encuentran en el directorio del pool `default` (`/var/lib/libvirt/images`).

Los ficheros de imágenes de discos que utilizan el formato `qcow2` tienen la característica de aprovisionamiento ligero, el fichero tiene un tamaño virtual (el que hemos indicado en su creación y el que verá la máquina virtual que lo utilice) y el espacio ocupado en el disco del host (que irá creciendo conforme vayamos guardando información en la imagen). Podemos ver esta característica ejecutando la siguiente instrucción:

```
usuario@kvm:~$ virsh vol-list default --details
 Name                       Path                                        Type   Capacity     Allocation
---------------------------------------------------------------------------------------------------------
 debian12.qcow2             /var/lib/libvirt/images/debian12.qcow2      file   10,00 GiB    2,47 GiB
 ...
```

Podemos obtener la información de un determinado volumen de un pool, ejecutando:

```
usuario@kvm:~$ virsh vol-info debian12.qcow2 default
Name:           debian12.qcow2
Type:           file
Capacity:       10,00 GiB
Allocation:     2,47 GiB
```

De la misma forma que los pools, los volúmenes están definidos en libvirt con el formato XML. Para ver la definición XML del volumen `debian12.qcow2` del pool `default`, podemos ejecutar `virsh vol-dumpxml debian12.qcow2 default`. A partir de un fichero XML con la definición de un nuevo volumen, podríamos crearlo con el comando `virsh vol-create`. **Nota: En este caso no existe el comandos `virsh vol-define`, ya que los volúmenes no se pueden crear temporalmente.**

**Nota: Para profundizar en el formato XML que define los volúmenes puedes consultar la documentación oficial: [Storage pool and volume XML format](https://libvirt.org/formatstorage.html).**

## Creación de nuevos volúmenes

Sin embargo, vamos a usar otro comando para crear volúmenes que nos permite indicar la información del nuevo volumen por medio de parámetros. Vamos a crear un nuevo volumen en el pool `vm-images`, cuyo nombre será `nuevodisco.img`, formato `raw` y tamaño de 10GB:

```
usuario@kvm:~$ virsh vol-create-as vm-images nuevodisco.img --format raw 10G 
```

Podemos comprobar que se ha creado un nuevo fichero de imagen:

```
usuario@kvm:~$ sudo ls -l /srv/images/
total 10485760
-rw------- 1 root root 10737418240 abr  3 07:17 nuevodisco.img
```

Podemos listar los volúmenes que hemos creado en el pool `vm-images`, y comprobar que no tenemos la característica de aprovisionamiento ligero al estar usando el formato `raw`:

```
usuario@kvm:~$ virsh vol-list vm-images --details
```

Para borrar el volumen, ejecutamos:

```
usuario@kvm:~$ virsh vol-delete nuevodisco.img vm-images
```

Tenemos a nuestra disposición más operaciones sobre los volúmenes, estudiaremos algunas de ellas en apartados posteriores: 

* `vol-clone`: para clonar el volumen.
* `vol-resize`: para redimensionar volúmenes.
* `vol-download`: para descargar el contenido de un volumen de almacenamiento y guardarlo en un fichero local.
* `vol-upload`: para cargar información a un volumen desde un fichero.

Hay que recordar que todas estas operaciones se realizan sobre volúmenes, y por tanto el medio de almacenamiento que gestionan dependerán del tipo del pool con el que estemos trabajando. De esta forma, por ejemplo:

* Si usamos `vol-create-as` en un pool de tipo **disk** se crearía una partición en un disco.
* Si usamos `vol-create-as` en un pool de tipo **logical** se crearía un volumen lógico LVM.

