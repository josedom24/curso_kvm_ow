# Instalación de QEMU/KVM + libvirt

Para trabajar con el sistema de virtualización QEMU/KVM + libvirt en nuestra distribución Linux Debian/Ubuntu, vamos a instalar los siguientes paquetes:

```
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients
```

* `qemu-kvm`: Virtualización con KVM.
* `libvirt-daemon-system`: Servicio libvirtd para gestionar VMs.
* `libvirt-clients`: Herramientas como `virsh` y `virt-install`.

Podemos obtener las versiones de las aplicaciones que hemos instalado (en mi caso en una distribución Linux Ubuntu 24.04) ejecutando:
```
virsh version
Compiled against library: libvirt 10.0.0
Using library: libvirt 10.0.0
Using API: QEMU 10.0.0
Running hypervisor: QEMU 8.2.2
```

## Tipos de conexiones a libvirt

libvirt proporciona varios mecanismos para conectarse a un hipervisor QEMU/KVM:

* **Conexión local no privilegiada a libvirt**: Esta conexión permite a un usuario sin privilegios gestionar máquinas virtuales en su propio entorno sin necesidad de permisos de root. En este modo los usuarios sin privilegios pueden gestionar máquinas virtuales, pero no tienen acceso a características avanzadas, por ejemplo la gestión de redes virtuales.

    * URL de conexión: `qemu:///session`.

* **Conexión local privilegiada a libvirt**: Este método permite a un usuario con permisos de superusuario administrar todas las máquinas virtuales del sistema. Es el modo más común en servidores o entornos de producción.

    * URL de conexión: `qemu:///system`.

* **Conexión remota a libvirt**: Este método permite administrar un hipervisor QEMU/KVM en otro equipo a través de la red. Se usa en entornos de gestión centralizada o administración remota. Se pueden usar varios protocolos para el acceso, pero el más común es ssh.

    * URL de conexión: `qemu+ssh://<usuario>@<dirección  máquina remota>/system`.