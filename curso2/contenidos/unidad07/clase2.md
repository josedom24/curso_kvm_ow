# Despliegue automatizado de máquinas virtuales con virt-builder

[`virt-builder`](https://libguestfs.org/virt-builder.1.html) es una herramienta para despliegues rápidos de nuevas máquinas virtuales. Además nos permite personalizar estas máquinas a través de plantillas de sistemas operativos editables, ahorrándonos el tiempo de hacer una instalación del sistema desde cero.

Para usar esta aplicación debemos instalar el paquete `libguestfs-tools` que ya lo habíamos instalado para trabajar con otras aplicaciones.

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
```
El funcionamiento de la herramienta es el siguiente: 

* Hemos indicado una **plantilla** llamada `debian-12` que se ha descargado de un repositorio y la ha descomprimido. Podemos ver todas las plantillas ejecutando `virt-builder -l`. Las plantillas descargadas se almacenan en caché en el directorio home del usuario actual con la siguiente ruta: `$HOME/.cache/virt-builder`. Podemos visualizar las plantillas que tenemos descargadas con la instrucción `virt-builder --print-cache` y borrar todas las plantillas que tenemos guardadas con `virt-builder --delete-cache`.
* A partir de la plantilla ha creado un fichero de imagen con el formato indicado (`--format`), lo ha redimensionado la imagen con `virt-resize` al tamaño que hemos indicado con el parámetro `size` y lo ha guardado en el path indicado con el parámetro `--output`.
* A continuación ha modificado la configuración de la imagen, aunque toda la configuración que hemos indicado no es imprescindible, hemos realizado los siguientes cambios:
    * Hemos indicado el hostname de la máquina (parámetro `--hostname`).
    * Hemos cambiado la contraseña del usuario `root` con `--root-password`. Tenemos distintos métodos para realizar el cambio y no indicar implícitamente la contraseña en la instrucción.
    * Con el parámetro `--run-command` podemos ejecutar comandos en la imagen. En este caso, hemos configurado el teclado en español y hemos modificado el fichero `/etc/network/interface` con el nombre que se le asigna a la interfaz de red (`enp1s0` en ves de `ens2`).
    * Con el parámetro `--firstboot-command` se configura instrucciones que se ejecutarán en el primer arranque de la máquina. En este caso, creamos el usuario `usuario` sin contraseña y obligamos a cambiarla la primera vez que accedamos con él, reconfiguramos el servidor SSH para que se generen las claves SSH del host y habilitamos la salida por el puerto serie.

En la documentación de la herramienta puedes encontrar muchas más opciones. Una vez terminada la ejecución de esta instrucción tenemos creado la imagen del disco en ` /var/lib/libvirt/images/mi_debian12.qcow2`.

## Creación de la máquina virtual con virt-install

Vamos a crear una máquina virtual con `virt-install` que use la imagen que hemos creado en el apartado anterior. Vamos a suponer que estamos en un servidor sin entorno gráfico y por tanto vamos a cceder a la máquina usando el puerto serie. Para crear la máquina:

```
usuario@kvm:~$ virt-install --connect qemu:///system \
                            --virt-type kvm \
                            --name debian12-builder \
                            --disk path=/var/lib/libvirt/images/mi_debian12.qcow2,format=qcow2 \
                            --os-variant debian12 \
                            --memory 2048 \
                            --vcpus 2 \
                            --import \
                            --noautoconsole
```

Con la opción `--noautoconsole` evitamos que `virt-install` intente conectar automática con la consola de la máquina virtual.

## Acceso a la máquina que hemos creado

Ahora que la máquina está funcionando podemos conectarnos a ella usando la consola serie. Usamos el usuario `usuario` y se nos pedirá que configuremos una nueva contraseña:

```
usuario@kvm:~$ virsh console debian12-builder 
Connected to domain 'debian12-builder'
Escape character is ^] (Ctrl + ])

mimaquina login: usuario
You are required to change your password immediately (administrator enforced).
New password: 
Retype new password: 
```

También podemos conectarnos por SSH a esta máquina:

```
usuario@kvm:~$ virsh domifaddr debian12-builder 
 Name       MAC address          Protocol     Address
-------------------------------------------------------------------------------
 vnet0      52:54:00:55:13:25    ipv4         192.168.122.26/24

usuario@kvm:~$ ssh usuario@192.168.122.26
...
```

