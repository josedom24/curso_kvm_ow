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

## Ejemplos de conexión a libvirt 

Vamos a usar la utilidad `virsh`, que nos proporciona una shell completa para el manejo de libvirt. Con el comando `list` mostramos las máquinas virtuales que hemos creado.

Con un usuario sin privilegios ejecutamos:

```
usuario@kvm:~$ virsh list
```

Estaríamos haciendo una conexión local con un usuario no privilegiado (estaríamos conectando con la URI `qemu:///session`) y estaríamos mostrando las máquinas virtuales de este usuario.

Si por el contrario, como `root` ejecutamos:

```
root@kvm:~# virsh list
```

Estaríamos haciendo una conexión local privilegiada (estaríamos conectando con la URI `qemu:///system`) y mostraríamos las máquinas virtuales del sistema.

Si queremos que un usuario sin privilegios pueda hacer conexiones privilegiadas, el usuario debe pertenecer el grupo `libvirt`. Para realizar esta comprobación ejecutamos la siguiente instrucción con nuestro usuario sin privilegio:

```
usuario@kvm:~$ group
```

Y comprobamos que en la lista de grupos aparece `libvirt`. Si no pertenece a dicho grupo lo añadimos:

```
usuario@kvm:~$ sudo usermod -aG libvirt $USER
```
Y finalmente reiniciamos la máquina para que tenga efecto dicho cambio.

Para que el usuario `usuario` haga una conexión privilegiada tendrá que indicar explícitamente la conexión a la URI `qemu:///system`:

```
usuario@kvm:~$ virsh -c qemu:///system list
```

Para no tener que especificar siempre el parámetro de conexión podemos crear una variable de entorno llamada `LIBVIRT_DEFAULT_URI` de la siguiente forma:

```
usuario@kvm:~$ export LIBVIRT_DEFAULT_URI='qemu:///system'
usuario@kvm:~$ virsh list
```

Nota: Suponemos que durante el curso, tendremos está variable definida y los comandos se ejecutarán desde un usuario sin privilegios.
