# Copia de seguridad de máquinas virtuales

La realización de copias de seguridad de máquinas virtuales es una tarea esencial en cualquier entorno de virtualización que busque garantizar la disponibilidad y recuperación de datos ante fallos, errores humanos o tareas de mantenimiento. En el caso de entornos basados en KVM/libvirt, una copia de seguridad completa de una máquina virtual debe incluir tanto **la definición XML de la máquina virtual como sus imágenes de disco**, ya que ambas partes son necesarias para restaurar una máquina virtual de forma fiel y funcional.

En este apartado vamos a mostrar cómo realizar una copia de seguridad de una máquina virtual ubicada en un servidor remoto, y cómo restaurarla en nuestro host local utilizando únicamente herramientas integradas en libvirt, sin depender de soluciones de terceros.

* Obtendremos la definición de la máquina virtual en formato XML mediante `virsh dumpxml`.
* Copiaremos las imágenes de disco asociadas con `virsh vol-download`. 
* Para restaurar la máquina virtual a partir de la definición XML utilizaremos `virsh define`.
* Y restauraremos la imagen de disco usando `virsh vol-upload`.

## Ejemplo de copia de seguridad

Vamos a realizar una copia de seguridad de la máquina `alpine` del servidor remoto y la vamos a restaurar en nuestro servidor local. Para ello realizaremos los siguientes pasos:

1. Guardamos la definición XML de la máquina a la que queremos realizar la copia de seguridad. En nuestro caso la máquina `alpine` del servidor remoto:

    ```
    usuario@kvm:~$ virsh -c qemu+ssh://jose@192.168.100.1/system dumpxml alpine > alpine.xml
    ```

2. Comprobamos el nombre del fichero de imagen de disco de la máquina virtual, para ello:

    ```
    usuario@kvm:~$ virsh -c qemu+ssh://jose@192.168.100.1/system domblklist alpine
    Target   Source
    ------------------------------------------------
     vda      /var/lib/libvirt/images/alpine.qcow2
    ```

3. Realizamos la copia del fichero de imagen de disco de la máquina virtual. En este ejemplo concreto el volumen que vamos a copiar está en un pool de almacenamiento llamado `images`. Podemos ver el volumen ejecutando:

    ```
    usuario@kvm:~$  virsh -c qemu+ssh://jose@192.168.100.1/system vol-list images
    ```
    Y realizamos la copia del volumen, ejecutando:

    ```
    usuario@kvm:~$ virsh -c qemu+ssh://jose@192.168.100.1/system vol-download alpine.qcow2 alpine-copia.qcow2 --pool images
    ```
    
    Podríamos guardar los dos ficheros que hemos generado: `alpine.xml` y `alpine-copia.qcow2` y estaríamos respaldando nuestra máquina virtual.

4. Ahora vamos a restaurar en nuestro servidor la máquina que acabamos de guardar. Vamos a asegurarnos de que estamos trabajando con la conexión privilegiada (`export LIBVIRT_DEFAULT_URI='qemu:///system'`). Además, para no tener que cambiar la definición de la máquina virtual vamos a copiar el fichero de imagen de disco con el mismo nombre. Para ello vamos a definir la máquina a partir del fichero XML y posteriormente vamos a recuperar la imagen de disco:

    ```
    usuario@kvm:~$ virsh define alpine.xml

    usuario@kvm:~$ virsh vol-create-as default alpine.qcow2 5G --format qcow2
    usuario@kvm:~$ virsh vol-upload --pool default alpine.qcow2 alpine-copia.qcow2
    ```

5. Comprobamos que la máquina ha sido restaurada con éxito.

    ```
    usuario@kvm:~$ virsh start alpine 
    usuario@kvm:~$ virsh console alpine 
    ```

    





