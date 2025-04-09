# Clonación de máquinas virtuales

La clonación de una máquina virtual copia la configuración XML de la máquina de origen y sus imágenes de disco, y realiza ajustes en las configuraciones para asegurar la unicidad de la nueva máquina. Esto incluye cambiar el nombre de la máquina y asegurarse de que utiliza los clones de las imágenes de disco. No obstante, los datos almacenados en los discos virtuales del clon son idénticos a los de la máquina de origen. 

La clonación nos permite crear nuevas máquinas de forma muy sencilla, sin necesidad de pasar por el proceso de instalación desde una imagen ISO.

Para realizar la clonación vamos a partir de una máquina virtual que esté apagada.

## Uso de virt-clone para realizar la clonación

Vamos a usar la aplicación `virt-clone` para realizar la clonación. Puedes profundizar en el uso de esta herramienta consultando la documentación oficial: [virt-clone](https://linux.die.net/man/1/virt-clone). Veamos algunos casos de uso:

```
usuario@kvm:~$ virt-clone --connect=qemu:///system --original debian12 --auto-clone
```

Es la forma más sencilla, se crea una nueva máquina. El parámetro `--auto-clone` asigna automáticamente:

* Un nuevo **nombre** para la máquina virtual clonada si no se especifica uno.
* Nuevas **direcciones MAC** para las interfaces de red para evitar conflictos en la red.
* Una nueva **ruta del disco para el almacenamiento** del clon, evitando sobrescribir el disco existente.

Si queremos indicar el nombre de la nueva máquina: usamos el parámetro `--name` y si queremos indicar el nombre del nuevo volumen usamos `--file`:

```
usuario@kvm:~$ virt-clone --connect=qemu:///system --original debian12 --name debian12-clon --file /var/lib/libvirt/images/vol_debian12_clon.qcow2 --auto-clone
```

## Uso de virsh para realizar la clonación

Otra estrategia para crear una nueva máquina virtual clonada a a partir de otra, es usar el comando `virsh vol-clone` para clonar el volumen de la máquina original y posteriormente crear una nueva máquina virtual con el volumen clonado.

```
usuario@kvm:~$ virsh vol-clone debian12.qcow2 vol_debian12_clon.qcow2 --pool default

usuario@kvm:~$ virt-install --connect qemu:///system \
                            --virt-type kvm \
                            --name debian12-clone \
                            --os-variant debian12 \
                            --disk vol=default/vol_debian12_clon.qcow2 \
                            --memory 2048 \
                            --vcpus 2 \
                            --import                             
```

Usamos la opción `--import` para que no te pida que indique el medio de instalación, simplemente va a usar el volumen indicado como disco de la máquina virtual.

## Las máquinas virtuales clonadas son iguales a las originales

La máquina clon que hemos creado es igual a la original. La nueva máquina contiene identificadores que deberían ser únicos (como el machine ID, claves SSH de host, hostname, ...).
Podemos acceder a la máquina y cambiar el fichero `/etc/hostname` para cambiar el nombre de la máquina, pero todavía tendríamos mucha información repetida entre las dos máquinas. 
Por lo tanto no vamos a realizar la clonación de esta manera. En el siguiente apartado vamos a aprender a crear **plantillas de máquinas virtuales** que nos permiten realizar la clonación de forma adecuada.
