# Características de las máquinas virtuales

Después de instalar nuestra primera máquina, podemos comprobar la lista de máquinas ejecutando la siguiente instrucción:

```
usuario@kvm:~$ virsh list
 Id   Name       State
--------------------------
 1    debian12   running
```

Sie estamos trabajando en un sistema con entorno gráfico podemos usar la herramienta `virt-viewer` para conectarno  a la consola de la máquina:

```
usuario@kvm:~$ virt-viewer debian12
```

## Red

Como comentábamos en el punto anterior, la máquina que hemos creado se conecta, por defecto, a la red `default`. Esta red es de tipo NAT, y comprobamos que la máquina ha recibido una IP de forma dinámica y que su puerta de enlace corresponde a la dirección IP `192.168.122.1`, que corresponde con el host, el servidor DNS corresponde a la misma IP y comprobamos que tiene resolución y acceso a internet:

```
usuario@debian12:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:66:e7:6a brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.151/24 brd 192.168.122.255 scope global dynamic enp1s0
       valid_lft 3581sec preferred_lft 3581sec
    inet6 fe80::5054:ff:fe66:e76a/64 scope link 
       valid_lft forever preferred_lft forever

usuario@debian12:~$ ip r
default via 192.168.122.1 dev enp1s0 
192.168.122.0/24 dev enp1s0 proto kernel scope link src 192.168.122.151 

usuario@debian12:~$ cat /etc/resolv.conf 
nameserver 192.168.122.1
```

## Recursos hardware

Podemos comprobar que la máquina tiene un disco de 10 Gb y de memoria RAM 1Gb:

```
usuario@debian12:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0     11:0    1 1024M  0 rom  
vda    254:0    0   10G  0 disk 
├─vda1 254:1    0    9G  0 part /
├─vda2 254:2    0    1K  0 part 
└─vda5 254:5    0  975M  0 part [SWAP]

usuario@debian12:~$ free -h
               total       usado       libre  compartido   búf/caché   disponible
Mem:           960Mi       203Mi       782Mi       4,5Mi        93Mi       757Mi
Inter:         974Mi          0B       974Mi
```

## Almacenamiento

Un **Pool de almacenamiento** es un recurso de almacenamiento. Lo más usual es tener pools de almacenamiento que sean locales, por ejemplo un directorio. Pode efecto tenemos el un pool llamado `default`, que corresponde con el directorio `/usr/lib/libvirt/images` y donde se guardarán los ficheros correspondientes a las imágenes de disco.

Podemos ver los pools de almacenamiento, que tenemos creado, ejecutando:

```
usuario@kvm:~$ virsh pool-list 
 Name     State    Autostart
------------------------------
 default   active   yes

```

Un **volumen** es un medio de almacenamiento que podemos crear en un pool de almacenamiento en kvm. Si el pool de almacenamiento es de tipo *dir*, entonces el volumen será un fichero de imagen.

Veamos el volumen que se ha creado el pool `default`:

```
usuario@kvm:~$ virsh vol-list default
 Name                               Path
----------------------------------------------------------------------------------------------
 debian-12.10.0-amd64-netinst.iso   /var/lib/libvirt/images/debian-12.10.0-amd64-netinst.iso
 debian12.qcow2                     /var/lib/libvirt/images/debian12.qcow2

```
En todos estos conceptos sobre almacenamiento profundizaremos en el módulo correspondiente.