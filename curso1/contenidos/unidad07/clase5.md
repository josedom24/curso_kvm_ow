# Gestión de máquinas virtuales con cockpit

**Cockpit** es una interfaz web ligera para administrar servidores Linux. Permite gestionar **servicios, almacenamiento, redes, contenedores y máquinas virtuales** de forma gráfica, sin necesidad de usar la terminal.  

Sus características principales son:

* Administración remota mediante navegador web.  
* Monitorización del uso de CPU, RAM, red y disco en tiempo real.  
* Gestión de usuarios, logs, actualizaciones y servicios del sistema.  
* Configuración de almacenamiento (LVM, RAID, discos, sistemas de archivos).  
* Soporte para contenedores (Docker y Podman).  
* Gestión de máquinas virtuales con KVM/Libvirt a través del complemento `cockpit-machines`.  

## Instalación de Cockpit


En sistemas basados en Debian/Ubuntu:  

```
sudo apt install cockpit cockpit-machines
```

Finalmente, accedemos desde un navegador en `https://<IP_DEL_SERVIDOR>:9090/`  

## Funcionalidades de Cockpit para KVM

* **Crear nuevas máquinas virtuales** con opciones de almacenamiento y red.  
* **Administrar discos, snapshots y configuraciones de las VMs**.  
* **Iniciar, detener, reiniciar y eliminar máquinas virtuales**.  
* **Conectar a la consola de una VM** sin necesidad de `virt-manager`.  
* **Gestionar redes y puentes virtuales (bridges)** para las VMs.  
* **Monitorizar el uso de CPU, RAM y almacenamiento de las VMs**.  

