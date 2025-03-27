# Creación de máquinas virtuales por red

`virt-install` nos permite instalar sistemas operativos en máquinas virtuales utilizando imágenes de instalación alojadas en servidores accesibles por red mediante HTTP, FTP o NFS. Este método es útil cuando no se quiere descargar manualmente una ISO o cuando se tiene un repositorio centralizado de imágenes.

Muchas distribuciones Linux nos ofrecen repositorios de instalación que son accesible desde distintas URL:

* **Debian**: http://deb.debian.org/debian/dists/bookworm/main/installer-amd64/
* **CentOS**: http://mirror.centos.org/centos/9-stream/BaseOS/x86_64/os/
* **Rocky Linux**: http://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/
* **AlmaLinux**: http://repo.almalinux.org/almalinux/9/BaseOS/x86_64/os/
* **Fedora**: https://download.fedoraproject.org/pub/fedora/linux/releases/41/Everything/x86_64/os/
* **Arch Linux**: http://mirror.rackspace.com/archlinux/iso/latest/

Para que una URL sea válida como fuente de instalación en red, debe contener una estructura específica con los archivos esenciales para el arranque y la instalación: el kernel del instalación, la imagen del disco RAM inicial que contiene el sistema mínimo necesario para comenzar la instalación,...

## Instalación en red con virt-install

Para realizar este tipo de instalación con `virt-install`vamos a indicar la URL de instalación en el parámetro `--location`. 

Además suponiendo que estamos trabajando en un sistema donde tenemos entorno gráfico, vamos a realizar la instalación conectándonos por la consola serie. Para ello:

* Indicamos que no tenemos consola gráfica, pero si consola serie: `--graphics none --console pty,target_type=serial`.
* Pasamos parámetros al kernel del sistema operativo para que envíe la salida por la consola serie: `--extra-args="console=ttyS0,115200n8"`.

La instrucción que vamos a ejecutar sería la siguiente:

```
usuario@kvm:~$ virt-install --connect qemu:///system \
                            --virt-type kvm \
                            --name debian12-red \
                            --location http://deb.debian.org/debian/dists/bookworm/main/installer-amd64/ \
                            --os-variant debian12 \
                            --disk size=10 \
                            --memory 1024 \
                            --vcpus 1 \
                            --graphics none --console pty,target_type=serial \
                            --extra-args="console=ttyS0,115200n8"
```

