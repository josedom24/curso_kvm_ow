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

Podemos cualquier estrategia de las que hemos estudiado para crear una maquina virtual. En este caso, vamos a usar `virt-builder` para crear una máquina muy liviana con la distribución `cirros`:

usuario@kvm:~$ sudo virt-builder cirros-0.3.5 \
                --format qcow2 \
                --output /home/usuario/.local/share/libvirt/images/cirros.qcow2 \

sudo chown usuario: /home/usuario/.local/share/libvirt/images/cirros.qcow2             

## Creación de la máquina virtual

La imagen `cirros` se suelen utilizar en entornos cloud para hacer comprobaciones por lo tanto la mejor manera de configurar es usar `cloud-init`. En este caso usamos el fichero `cloud.yaml`:

```yaml
#cloud-config
seña a los usuarios creados
chpasswd:
  expire: False
  users:
    - name: root
      password: asdasd
      type: text
```

usuario@kvm:~$ virt-install --connect qemu:///session \
                            --virt-type kvm \
                            --name cirros-usuario \
                            --disk path=/home/usuario/.local/share/libvirt/images/cirros.qcow2 \
                            --os-variant cirros0.3.5 \
                            --memory 512 \
                            --vcpus 1 \
                            --import \
                            --cloud-init user-data=cloud.yaml \
                            --noautoconsole


# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast qlen 1000
    link/ether 52:54:00:64:d6:ed brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global eth0
    inet6 fec0::5054:ff:fe64:d6ed/64 scope site dynamic 
       valid_lft 86241sec preferred_lft 14241sec
    inet6 fe80::5054:ff:fe64:d6ed/64 scope link 
       valid_lft forever preferred_lft forever
# ip r
default via 10.0.2.2 dev eth0 
10.0.2.0/24 dev eth0  src 10.0.2.15 
# cat /etc/resolv.conf 
nameserver 10.0.2.3
