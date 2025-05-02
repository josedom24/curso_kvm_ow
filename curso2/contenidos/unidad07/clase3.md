# Despliegue automatizado de máquinas virtuales usando cloud-init

Otro enfoque muy eficiente para crear máquinas virtuales de forma automática es el uso de **imágenes cloud** junto con **cloud-init**, una herramienta diseñada para la inicialización y configuración automática de instancias en su primer arranque.

Las **imágenes cloud** son discos base preconfigurados, optimizados para ser utilizados en entornos virtualizados o en la nube. Estas imágenes suelen estar diseñadas para un arranque rápido y para su personalización dinámica mediante herramientas de automatización. A diferencia de las imágenes tradicionales, no están configuradas con parámetros específicos de red, usuarios o almacenamiento, sino que dependen de herramientas como `cloud-init` para adaptar la configuración en el primer arranque.

`cloud-init` es un conjunto de scripts y herramientas que permiten la personalización automática de instancias o máquinas virtuales en su primer arranque. Originalmente desarrollado para entornos en la nube como OpenStack, AWS y Azure, `cloud-init` se ha convertido en una solución estándar para la inicialización de sistemas operativos en cualquier entorno virtualizado.

Su función principal es leer **datos de configuración** desde diversas fuentes y aplicarlos al sistema operativo en el arranque inicial. Esto permite realizar tareas como:

* Configuración de red.
* Creación de usuarios y claves SSH.
* Instalación de paquetes.
* Ejecución de comandos personalizados.
* Configuración de particiones y almacenamiento.
* ...

## Configuración de cloud-init

En la [documentación](https://cloudinit.readthedocs.io/en/latest/) de `cloud-init` puedes ver todas las configuraciones que podemos realizar, para este ejercicio vamos a realizar un ejemplo sencillo. Para indicar la configuración se suele usar un fichero con formato YAML conocido como `cloud-config`. Creamos un fichero `cloud.yaml` con el siguiente contenido:

```yaml
#cloud-config

# Configuramos el nombre de la máquina
hostname: ubuntu-vm

# Actualiza los paquetes
package_update: true
package_upgrade: true
# Cambia las contraseña a los usuarios creados
chpasswd:
  expire: False
  users:
    - name: root
      password: newpassword
      type: text
    - name: ubuntu
      password: asdasd
      type: text
```

Con este fichero se van a hacer tres configuraciones en la máquina: se cambia su nombre, se actualizan los paquetes y finalmente, se cambian las contraseñas de los usuarios `root` y `ubuntu`. Usamos el usuario `ubuntu` porque vamos a usar una imagen cloud de Ubuntu que suelen tener preconfigurados un usuario llamado `ubuntu`.

## Descargar de la imagen cloud

La mayoría de las distribuciones Linux nos ofrecen un repositorio de descargas de imágenes cloud:

* Ubuntu Cloud Images: https://cloud-images.ubuntu.com
* Debian Cloud Images: https://cloud.debian.org/images/cloud/
* Fedora Cloud Images: https://alt.fedoraproject.org/cloud/
* CentOS Cloud Images: https://cloud.centos.org/centos/
* AlmaLinux Cloud Images: https://repo.almalinux.org/cloud/
* Rocky Linux Cloud Images: https://dl.rockylinux.org/pub/rocky/
* openSUSE Cloud Images: https://download.opensuse.org/repositories/Cloud:/Images:/
* Arch Linux Cloud Images (comunidad): https://gitlab.archlinux.org/archlinux/arch-boxes
* Amazon Linux Cloud Images: https://cdn.amazonlinux.com/os-images/latest/

Nosotros vamos a trabajar con una imagen cloud de Ubuntu. Descargamos la imagen cloud y la guardamos en el directorio de trabajo:

```
usuario@kvm:~$ wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img
usuario@kvm:~$ sudo mv noble-server-cloudimg-amd64.img /var/lib/libvirt/images
```

## Creación de la imagen por clonación enlazada

Para reutilizar la imagen que hemos descargado, vamos a crear una clonación enlazada para crear la nueva imagen de la máquina que vamos a crear:

```
usuario@kvm:~$ cd /var/lib/libvirt/images
usuario@kvm:~$ sudo qemu-img create -f qcow2 -b noble-server-cloudimg-amd64.img -F qcow2 ubuntu2404.qcow2
```

Y si queremos podemos cambiarle el tamaño, ya que al ejecutar la máquina por primera vez con la imagen cloud el sistema operativo se redimensionará al tamaño del disco:

```
usuario@kvm:~$ sudo qemu-img resize ubuntu2404.qcow2 20G
```

## Creación de la máquina virtual

Para crear la máquina virtual, ejecutamos:

```
usuario@kvm:~$ virt-install --connect qemu:///system \
                            --virt-type kvm \
                            --name ubuntu-vm \
                            --memory 2048 \
                            --vcpus 2 \
                            --os-variant ubuntu24.04 \
                            --disk path=/var/lib/libvirt/images/ubuntu2404.qcow2,format=qcow2,bus=virtio \
                            --import \
                            --cloud-init user-data=/var/lib/libvirt/images/cloud.yaml \
                            --noautoconsole
```
Hemos usado el parámetro `--cloud-init` para indicar el fichero cloud-config que vamos a usar para realizar la configuración.

Ahora que la máquina está funcionando podemos conectarnos a ella usando la consola serie. Usamos el usuario `ubuntu` con la contraseña que hemos configurado.

```
usuario@kvm:~$ virsh console ubuntu-vm 
Connected to domain 'ubuntu-vm'
Escape character is ^] (Ctrl + ])

ubuntu-vm login: ubuntu
Password: 
ubuntu@ubuntu-vm:~$
```
