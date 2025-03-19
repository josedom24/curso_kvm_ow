# Definición de una máquina virtual con virsh

En `virsh`, las máquinas virtuales se gestionan como **dominios**. Un dominio es una instancia de una máquina virtual administrada por Libvirt, con una configuración definida en un archivo XML.  

## Creación de un dominio con virsh
Para definir una máquina virtual con `virsh`, primero se crea un archivo XML con la configuración del dominio. Este archivo describe aspectos como la cantidad de CPU, memoria, almacenamiento y red.  

Archivo XML:

```xml
<domain type='kvm'>
  <name>mi-vm</name>
  <memory unit='MiB'>2048</memory>
  <vcpu placement='static'>2</vcpu>
  <os>
    <type arch='x86_64' machine='pc-q35-6.2'>hvm</type>
    <boot dev='hd'/>
  </os>
  <devices>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/mi-vm.qcow2'/>
      <target dev='vda' bus='virtio'/>
    </disk>
    <interface type='network'>
      <source network='default'/>
      <model type='virtio'/>
    </interface>
  </devices>
</domain>
```

## Definir e iniciar un dominio con virsh  
Una vez creado el archivo XML, se usa el siguiente comando para **definir** la máquina virtual en Libvirt:  
```bash
virsh define vm.xml
```
Esto registra el dominio, pero no lo inicia. Para iniciarlo, usa:  
```bash
virsh start mi-vm
```

Si se desea crear un dominio de manera temporal (no persistente), se puede usar:  
```bash
virsh create vm.xml
```
Este dominio existirá solo hasta que se apague.

