# Conexión local no privilegiada a libvirt

Durante todo el curso hemos hecho uso de la conexión privilegiada a libvirt y por tanto es el superusuario el que ha gestionado los recursos virtualizados.

En este apartado vamos a comprobar que un usuario sin privilegio puede crear sus máquinas virtuales. Para ello realizará una conexión local a libvirt usando la URI `qemu:///session`. En este modo de conexión, el usuario no tiene permisos para crear conexiones de red, por lo que se limita su uso de la red no privilegiada de QEMU ([SLIRP](https://wiki.qemu.org/Documentation/Networking#User_Networking_.28SLIRP.29)) que es útil para casos simples, pero que tiene bajo rendimiento y es poco configurable. 

Vamos asegurarnos que la variable de entorno `LIBVIRT_DEFAULT_URI` no la tenemos definida:

```
usuario@kvm:~$ unset LIBVIRT_DEFAULT_URI
```

Desde este momento el usuario sin privilegio hace una conexión no privilegiada usando la URL `qemu:///sesion`:

```
usuario@kvm:~$ virsh list --all
 Id   Name   State
--------------------
```

Podemos comprobar que no tenemos ninguna red ni ningún pool de almacenamiento:

```
usuario@kvm:~$ virsh net-list --all
 Name   State   Autostart   Persistent
----------------------------------------

usuario@kvm:~$ virsh pool-list --all
 Name   State   Autostart
---------------------------
```

El usuario sin privilegios no puede crear redes y la máquina que creemos se conectará a la red de usuario de QEMU. Aunque no es necesario y sabiendo que el directorio de imágenes del usuario suele ser `/home/usuario/.local/share/libvirt/images` podemos crear un pool de almacenamiento que llamaremos `default`:

```
usuario@kvm:~$ virsh pool-define-as default dir --target /home/usuario/.local/share/libvirt/images
usuario@kvm:~$ virsh pool-build default
usuario@kvm:~$ virsh pool-start default 
usuario@kvm:~$ virsh pool-autostart default 
```

## Creación de una imagen local

Podemos cualquier estrategia de las que hemos estudiado para crear una maquina virtual. En este caso, vamos a usar una imagen cloud de Debian:

```
usuario@kvm:~$ wget http://cloud.debian.org/images/cloud/bookworm/latest/debian-12-generic-amd64.qcow2 \
                    -O /home/usuario/.local/share/libvirt/images/debian12-cloud.qcow2
```


## Creación de la máquina virtual

Vamos a usar `cloud-init` para la configuración de la máquina. En este caso usamos el fichero `cloud.yaml`:

```yaml
#cloud-config

# Configuramos el nombre de la máquina
hostname: debian-usuario


# Cambia las contraseña a los usuarios creados
chpasswd:
  expire: False
  users:
    - name: root
      password: asdasd
      type: text
    - name: debian
      password: asdasd
      type: text
```

Y creamos la nueva máquina en nuestro entorno local:

```
usuario@kvm:~$ virt-install --connect qemu:///session \
                            --virt-type kvm \
                            --name debian-usuario \
                            --disk path=/home/usuario/.local/share/libvirt/images/debian12-cloud.qcow2 \
                            --os-variant debian12 \
                            --memory 1024 \
                            --vcpus 1 \
                            --import \
                            --cloud-init user-data=./cloud.yaml \
                            --noautoconsole
```

A continuación accedemos a la máquina con una conexión serie:

```
usuario@kvm:~$ virsh console debian-usuario
```

Y podemos comprobar que la máquina virtual se conecta a la red de usuario de QEMU ([SLIRP](https://wiki.qemu.org/Documentation/Networking#User_Networking_.28SLIRP.29)) que configura la máquina con la dirección IP `10.0.2.15`, su puerta de enlace, que es el anfitrión (la máquina física) es la dirección IP `10.0.2.2` y configura un servidor DNS en la dirección IP `10.0.2.3`. Esta red permite que la máquina tenga acceso a internet, pero no tendrá conectividad con el anfitrión u otras máquinas que creemos.

```
debian@debian-usuario:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:ad:25:fc brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 metric 100 brd 10.0.2.255 scope global dynamic enp1s0
       valid_lft 86380sec preferred_lft 86380sec
    inet6 fec0::5054:ff:fead:25fc/64 scope site dynamic mngtmpaddr noprefixroute 
       valid_lft 86383sec preferred_lft 14383sec
    inet6 fe80::5054:ff:fead:25fc/64 scope link 
       valid_lft forever preferred_lft forever

debian@debian-usuario:~$ ip r
default via 10.0.2.2 dev enp1s0 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev enp1s0 proto kernel scope link src 10.0.2.15 metric 100 
10.0.2.2 dev enp1s0 proto dhcp scope link src 10.0.2.15 metric 100 
10.0.2.3 dev enp1s0 proto dhcp scope link src 10.0.2.15 metric 100 

debian@debian-usuario:~$ cat /etc/resolv.conf 
...
nameserver 10.0.2.3
search .
```

Para terminar podemos comprobar que se ha creado el volumen que corresponde al disco de la máquina en el grupo de almacenamiento `default` de la conexión no privilegiada.

```
usuario@kvm:~$ virsh vol-list default
 Name                   Path
----------------------------------------------------------------------------------------
 debian12-cloud.qcow2   /home/usuario/.local/share/libvirt/images/debian12-cloud.qcow2
```