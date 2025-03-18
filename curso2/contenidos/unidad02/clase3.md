# Recursos disponibles en QEMU/KVM + libvirt

En este curso vamos a trabajar realizando conexiones locales privilegiadas, por lo tanto si queremos ver todas las máquinas creadas en el sistema debemos ejecutar:

```
usuario@kvm:~$ export LIBVIRT_DEFAULT_URI='qemu:///system'
usuario@kvm:~$ virsh list --all
```

La opción `--all` muestra las máquinas que se están ejecutando y las que están paradas.

## Redes disponibles

Cuando instalamos QEMU/KVM + libvirt se crea una red por defecto de tipo NAT. Para verla, ejecutamos la siguiente instrucción:

```
usuario@kvm:~$ irsh net-list --all
 Name      State    Autostart   Persistent
--------------------------------------------
 default   active   yes         yes

```
La opción `--all` muestra las redes activas e inactivas.

Si el estado estuviera en inactivo, para iniciarla, ejecutaríamos:

```
usuario@kvm:~$ virsh net-start default 
La red default se ha iniciado
```

Si fuera necesario, es recomendable activar la propiedad de **Incio autómatico**, para que se inicie de forma automática después de reiniciar el host, para ello:

```
usuario@kvm:~$ virsh net-autostart default
La red default ha sido marcada para iniciarse automáticamente
```

Aunque estudiaremos la redes con profundidad en el módulo correspondiente, podemos señalar que las máquinas virtuales que se conecten a esta red, tendrán las siguientes características:

* Tomarán una dirección IP de forma dinámica en el rango `192.168.122.2` - `192.168.122.254`. Es decir, existe un servidor DHCP (que se encuentra en el host) asignando de forma dinámica el direccionamiento.
* La puerta de enlace será la dirección IP `192.168.122.1` que corresponde al host. Está dirección también corresponde al servidor DNS que tiene configurado (que también se encuentra en el host).
* La máquina virtual estará conectada a un Linux Bridge (switch virtual) llamado `virbr0` por la que se conectará al host.
* El host hará de router/nat para que la máquina tenga conectividad al exterior.

Por defecto, las nuevas máquinas que creemos se conectarán a esta red.

## Almacenamiento disponible

Estudiaremos en profundidad el almacenamiento con el que podemos trabajar en el módulo correspondiente. En este momento, indicar que los ficheros correspondientes a las imágenes de discos de las nuevas máquinas virtuales que creemos se guardarán, por defecto, en el directorio `/var/lib/libvirt/images`.