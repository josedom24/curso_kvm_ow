# Despliegue automatizado de máquinas virtuales con virt-builder

[`virt-builder`](https://libguestfs.org/virt-builder.1.html) es una herramienta para un rápido despliegue de nuevas máquinas virtuales. Además nos permite personalizar estas máquinas a través de plantillas de sistemas operativos editables, ahorrándonos el tiempo de hacer una instalación del sistema desde cero.

Esta herramienta es provista por el paquete `libguestfs-tools` que ya lo ahbíamos instalado para trabajar con otras aplicaciones.

## Creación de imágenes con virt-builder

Para crear una nueva imagen de disco construida con `virt-builder` ejecutamos la siguiente instrucción:

```
usuario@kvm:~$ sudo virt-builder debian-12 \
                --size 10G \
                --format qcow2 \
                --output /var/lib/libvirt/images/mi_debian12.qcow2 \
                --hostname mimaquina \
                --root-password password:asdasd \
                --run-command "sed -i 's/^XKBLAYOUT=.*/XKBLAYOUT=\"es\"/' /etc/default/keyboard" \
                --run-command "dpkg-reconfigure -f noninteractive keyboard-configuration" \
                --run-command "sed -i 's/ens2/enp1s0/g' /etc/network/interfaces" \
                --firstboot-command 'useradd -m -p "" -s /bin/bash usuario ; chage -d 0 usuario' \
                --firstboot-command "dpkg-reconfigure openssh-server" \
                --firstboot-command "systemctl enable --now getty@ttyS0" 