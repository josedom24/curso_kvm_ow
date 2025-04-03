# Introducción al almacenamiento en KVM/libvirt

El almacenamiento es un aspecto fundamental en la virtualización, ya que permite gestionar el espacio en disco de las máquinas virtuales. En KVM/libvirt, el almacenamiento se organiza en **grupos o pools de almacenamiento (storage pools)** y **volúmenes de almacenamiento (storage volumes)**.

* **Pools de Almacenamiento**: Es una fuente de almacenamiento, una cantidad de almacenamiento que el administrador del host ha configurado para su uso por las máquinas virtuales. 
* **Volúmenes**: Los pools de almacenamiento se dividen en volúmenes. Cada uno de estos volúmenes lo utilizaran las máquinas virtuales como discos (dispositivos de bloques). 

## Pools de almacenamiento

Los pools de almacenamiento son espacios lógicos donde se almacenan las imágenes de disco de las máquinas virtuales. Existen diferentes tipos de almacenamiento compatibles con KVM/libvirt, que se pueden clasificar en dos categorías principales:

* **Almacenamiento basado en dispositivos de bloques**: Estos métodos proporcionan acceso directo a dispositivos de almacenamiento de bloques, como discos físicos o volúmenes lógicos. Algunos ejemplos son:

    * **disk**: Particiones o discos físicos completos.
    * **iscsi**: Dispositivos de almacenamiento compartido a través de iSCSI.
    * **logical**: Volúmenes lógicos de LVM.
    * **zfs**: Sistemas de almacenamiento avanzados que pueden gestionar volúmenes en bruto y proporcionar snapshots.

* **Almacenamiento basado en sistemas de ficheros**: Estos métodos almacenan las imágenes de disco como archivos dentro de un sistema de ficheros. Algunos ejemplos son:

    * **dir**: Directorios locales donde se almacenan ficheros de imagen de disco (el que usaremos en este curso).
    * **nfs**: Sistema de fichero compartido en red desde un servidor NFS.
    * **glusterfs**: Sistema distribuido de almacenamiento en red.

Hemos puestos algunos ejemplos de tipo de almacenamiento, pero en la [documentación](https://libvirt.org/storage.html) de libvirt puedes encontrar todas las fuentes de almacenamiento.

## Volúmenes de almacenamiento (Storage Volumes)

Los volúmenes son las unidades individuales de almacenamiento dentro de un pool. Según el tipo del pool de almacenamiento con el que estemos trabajando, el volumen corresponderá a elementos diferentes:
* En el caso del tipo **dir**, estos volúmenes corresponden a ficheros de imagen de disco. Los formatos de imagen de disco más utilizados son:
    * **raw**:  el formato raw es una imagen binaria sencilla de la imagen del disco. Se ocupa todo el espacio que hayamos indicado al crearla. El acceso es más eficiente. No soporta ni snapshots ni aprovisionamiento ligero.
    * **qcow2**: formato QEMU copy-on-write. Al crearse sólo se ocupa el espacio que se está ocupando con los datos (aprovisionamiento ligero), el fichero irá creciendo cuando escribamos en el él. Acepta instantáneas o snapshots. Es menos eficiente en cuanto al acceso.
    * **vdi, vmdk,...**: formatos de otros sistemas de virtualización.
* En el caso del tipo **logical** estos volúmenes serán volúmenes lógicos LVM.
* En el caso de tipo **disk** los volúmenes serán particiones de un disco.
* ...

Tenemos dos enfoques para gestionar los volúmenes:

* Usar **la API de libvirt**, es decir, usar herramientas como `virsh` para gestionar los volúmenes. En este caso, si creamos un volumen en un pool de tipo **dir**, estaríamos creando un fichero de imagen de disco. Del mismo modo, si lo creamos en un pool de tipo **logical** estaríamos creando un volumen lógico LVM.
* Utilizar herramientas específicas para crear los medios de almacenamiento y posteriormente **refrescar** el pool para que añada el nuevo volumen. Ejemplo: podemos usar la herramienta `qemu-img` para la creación de un fichero de imagen de disco y posteriormente actualizaremos el pool de tipo **dir** para añadir el nuevo volumen que corresponde al fichero que hemos creado. Otro ejemplo: usar la línea de comandos de LVM, creando un volumen lógico con el comando `lvcreate` y posteriormente actualizamos el pool de tipo **logical** para añadir el nuevo volumen.

Si estamos trabajando localmente en un servidor donde tenemos QEMU/KVM + libvirt instalado, no hay muchas diferencias de usar una y otra opción. El uso de la API de libvirt puede ser más interesante si estamos conectados a la API de libvirt de forma remota, ya que al gestionar los volúmenes estaríamos gestionando los recursos de almacenamiento (ficheros, volúmenes lógicos,...) sin necesidad de acceder al servidor y crearlos con herramientas específicas.

## Otras características que ofrecen los tipos de almacenamiento

* **Snapshots**: Los snapshots permiten capturar el estado de una máquina virtual en un momento determinado. Esto resulta útil para:

    * **Recuperación ante fallos**: Volver a un estado anterior en caso de errores.
    * **Pruebas y desarrollo**: Permitir experimentación sin afectar los datos originales.

    Los snapshots pueden ser a nivel de almacenamiento (ej. LVM, ZFS) o a nivel de imagen de disco (qcow2).

* **Aprovisionamiento ligero (Thin Provisioning)**: El aprovisionamiento ligero permite asignar espacio de almacenamiento de manera dinámica según sea necesario, en lugar de reservarlo completamente desde el principio. Esto optimiza el uso del espacio en disco. El formato **qcow2** soporta aprovisionamiento ligero, mientras que **raw** no lo hace.
