# Conexión local privilegiada a libvirt

En este curso vamos a trabajar realizando conexiones locales privilegiadas, por lo tanto, si queremos ver todas las máquinas creadas en el sistema debemos ejecutar:

```
usuario@kvm:~$ export LIBVIRT_DEFAULT_URI='qemu:///system'
usuario@kvm:~$ virsh list --all
```

La opción `--all` muestra las máquinas que se están ejecutando y las que están paradas.

## Redes disponibles

Cuando instalamos QEMU/KVM + libvirt se crea una red por defecto de tipo NAT. Para verla, ejecutamos la siguiente instrucción:

```
usuario@kvm:~$ virsh net-list --all
 Name      State    Autostart   Persistent
--------------------------------------------
 default   active   yes         yes

```
La opción `--all` muestra las redes activas e inactivas.

Si el estado estuviera en inactivo, para iniciarla, ejecutaríamos:

```
usuario@kvm:~$ virsh net-start default 
```

Si fuera necesario, es recomendable activar la propiedad de **Inicio automático**, para que se inicie de forma automática después de reiniciar el host, para ello:

```
usuario@kvm:~$ virsh net-autostart default
```

Podemos ver la definición de esta red en formato XML, ejecutando:

```
usuario@kvm:~$ virsh net-dumpxml default
```

Aunque estudiaremos la redes con profundidad en el módulo correspondiente, podemos señalar que las máquinas virtuales que se conecten a esta red, tendrán las siguientes características:

* Tomarán una dirección IP de forma dinámica en el rango `192.168.122.2` - `192.168.122.254`. Es decir, existe un servidor DHCP (que se encuentra en el host) asignando de forma dinámica el direccionamiento.
* La puerta de enlace será la dirección IP `192.168.122.1` que corresponde al host. Está dirección también corresponde al servidor DNS que tiene configurado (que también se encuentra en el host).
* La máquina virtual estará conectada a un Linux Bridge (switch virtual) llamado `virbr0` por la que se conectará al host.
* El host hará de router/nat para que la máquina tenga conectividad al exterior.

Por defecto, las nuevas máquinas que creemos se conectarán a esta red.

## Almacenamiento disponible

Las imágenes de discos de las nuevas máquinas virtuales que creemos se guardarán, por defecto, en el directorio `/var/lib/libvirt/images`.

Un **Pool de almacenamiento** es un recurso de almacenamiento. Lo más usual es tener pools de almacenamiento que sean locales, por ejemplo un directorio.

Antes de empezar a trabajar con las máquinas virtuales vamos a crear un pool de almacenamiento que llamaremos `default`, que será de tipo *directorio* y que corresponderá al directorio que hemos indicado. Para crear este pool, ejecutaremos:

```
usuario@kvm:~$ virsh pool-define-as default dir --target /var/lib/libvirt/images
```

A continuación lo iniciamos y lo configuramos para que se autoinicie después de un reinicio:

```
usuario@kvm:~$ virsh pool-start default 
usuario@kvm:~$ virsh pool-autostart default 
```

Podemos comprobar que hemos creado el pool, ejecutando:

```
usuario@kvm:~$ virsh pool-list
```

Estudiaremos en profundidad el almacenamiento con el que podemos trabajar en el módulo correspondiente. 