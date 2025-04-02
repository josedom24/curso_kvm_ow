# Definición de un dominio con virsh

En `virsh`, las máquinas virtuales se gestionan como **dominios**. Un dominio es una instancia de una máquina virtual administrada por libvirt, con una configuración definida en un archivo XML.  

## Creación de un dominio con virsh
Para definir una máquina virtual con `virsh`, primero se crea un archivo XML con la configuración del dominio. Este archivo describe aspectos como la cantidad de CPU, memoria, almacenamiento y red.  

Hemos copiado al directorio `/var/lib/libvirt/images` la imagen ISO del sistema operativo que queremos instalar y vamos a crear un fichero de imagen de disco en el pool de almacenamiento `default`, este fichero corresponderá al disco de la máquina virtual. Aunque posteriormente estudiaremos el apartado de almacenamiento con profundidad, en este momento ejecutamos la siguiente instrucción para crear un disco de 10G:

```
usuario@kvm:~$ virsh vol-create-as default mi-vm.qcow2 --format qcow2 10G 
```

El archivo XML con el que vamos a trabajar se llama `dominio.xml` y tiene el siguiente contenido:

```xml
<domain type='kvm'>
  <name>mi-vm</name>
  <memory unit='MiB'>2048</memory>
  <vcpu placement='static'>2</vcpu>
  <os>
    <type arch='x86_64' machine='pc-q35-6.2'>hvm</type>
    <boot dev='cdrom'/>
    <boot dev='hd'/>
  </os>
  <devices>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/mi-vm.qcow2'/>
      <target dev='vda' bus='virtio'/>
    </disk>
    <disk type='file' device='cdrom'>
      <driver name='qemu' type='raw'/>
      <source file='/var/lib/libvirt/images/debian-12.10.0-amd64-netinst.iso' index='1'/>
      <target dev='sda' bus='sata'/>
      <readonly/>
    </disk>
    <interface type='network'>
      <source network='default'/>
      <model type='virtio'/>
    </interface>
    <graphics type='spice' autoport='yes'/>
  </devices>
</domain>
```

Hemos definido una máquina virtual con las siguientes características:

1. La máquina se llama `mi-mv`.
2. Tiene 2G de RAM y 2 vcpu.
3. El orden de arranque de dispositivo es, primer el CDROM y luego el disco duro.
4. Tiene dos dispositivos de almacenamiento: el disco principal que corresponde al fichero que hemos creado anteriormente y un CDROM que corresponde a la imagen ISO del sistema que vamos a instalar.
5. Tiene una tarjeta de red conectada a la red `default`.
6. Tiene una consola gráfica para que nos podamos conectar usando la aplicación `virt-viewer`.

## Definir e iniciar un dominio con virsh  

Una vez creado el archivo XML, se usa el siguiente comando para **definir** la máquina virtual en libvirt:  

```
usuario@kvm:~$ virsh define dominio.xml
```
Esto registra el dominio, pero no lo inicia. Para iniciarlo, usa:  
```
usuario@kvm:~$ virsh start mi-vm
```
Y para acceder a él podemos usar la aplicación `virt-viewer`, despues de instalar el paquete `virtinst`:

```
usuario@kvm:~$ sudo apt install virtinst
usuario@kvm:~$ virt-viewer mi-vm
```

Si se desea crear un dominio de manera temporal (no persistente), se puede usar:  
```
usuario@kvm:~$ virsh create dominio.xml
```
Este dominio existirá solo hasta que se apague.

