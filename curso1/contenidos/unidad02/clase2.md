# Instalación de virt-manager

[virt-manager](https://virt-manager.org/) es una aplicación gráfica de escritorio para gestionar máquinas virtuales a través de libvirt. Presenta una vista resumida de las máquinas virtuales en ejecución, su rendimiento en vivo y las estadísticas de utilización de recursos. Los usuarios pueden crear nuevas máquinas virtuales y configurarlas y gestionar sus dispositivos de hardware. Además, poseer un cliente VNC / SPICE que permite el acceso de forma sencilla a la consola de la máquina.

En sistemas operativos basados en Debian / Ubuntu, simplemente ejecutamos:

```
usuario@kvm:~$ sudo apt install virt-manager
```

Por dependencias se instalarán los paquetes necesarios de QEMU, KVM y libvirt.

## Configuración inicial

Como veremos a continuación, vamos a usar una conexión local privilegiada a libvirt, es decir, vamos a gestionar los recursos virtualizados usando el usuario con privilegios.

Para que nuestro usuario sin privilegio pueda usar este tipo de conexión debemos asegurarnos de que pertenece al grupo `libvirt`, para ello ejecutamos:
```
usuario@kvm:~$ sudo usermod -aG libvirt $USER
```

Y reiniciamos el sistema.

## Vista general de virt-manager

![img](img/virt-manager.png)

