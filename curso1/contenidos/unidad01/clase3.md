# Introducción a QEMU/KVM y libvirt

## QEMU/KVM 

De acuerdo con la [wiki de QEMU](https://wiki.qemu.org/Main_Page), "[QEMU](https://www.qemu.org/) es un emulador genérico y de código abierto de máquinas virtuales." Puede utilizarse como:
* Un **emulador**, permitiendo ejecutar sistemas operativos diseñados para una arquitectura diferente a la del hardware físico (por ejemplo, ejecutar un sistema ARM en una máquina x86_64). 
* Una **solución de virtualización completa**, cuando se combina con hipervisores como **KVM**, lo que permite aprovechar las extensiones de virtualización del procesador para lograr un rendimiento casi nativo.  

[KVM](https://www.linux-kvm.org/page/Main_Page) (**Kernel-based Virtual Machine**) es un hipervisor de tipo 1 integrado en el kernel de Linux. Ofrece una solución de virtualización completa en plataformas que cuentan con soporte de virtualización por hardware, como **Intel VT-x** y **AMD-V**. Se compone de un módulo del kernel (`kvm.ko`), que proporciona la infraestructura de virtualización, y un módulo específico para cada tipo de procesador (`kvm-intel.ko` o `kvm-amd.ko`).  

Al combinar **QEMU con KVM**, se puede ejecutar máquinas virtuales con sistemas operativos sin modificar, logrando un rendimiento significativamente superior al de una emulación pura. Cada máquina virtual dispone de su propio hardware virtualizado, como tarjetas de red, discos duros y tarjetas gráficas.  

### Dispositivos paravirtualizados

Al crear una máquina virtual, además de definir recursos como la cantidad de RAM, el espacio de almacenamiento y el número de vCPUs, es necesario seleccionar los dispositivos que la compondrán: interfaz de red, controladores de disco, tarjeta gráfica, etc.  

Por defecto, en un entorno de **virtualización completa** como QEMU/KVM, todos los dispositivos son **emulados por software**. Esto significa que la máquina virtual interactúa con ellos como si fueran dispositivos físicos reales. Por ejemplo, una interfaz de red puede emular una tarjeta **Realtek 8139**, o un disco virtual puede conectarse mediante una interfaz **IDE emulada**.  

Este enfoque tiene la ventaja de ofrecer **compatibilidad con una amplia gama de sistemas operativos**, ya que los dispositivos emulados suelen imitar hardware comúnmente soportado. Sin embargo, también presenta desventajas:  
* **Mayor uso de CPU** debido a la sobrecarga de la emulación.  
* **Mayor latencia de E/S**, lo que afecta al rendimiento de la red y del almacenamiento.  

Para solucionar estos problemas, **KVM proporciona dispositivos paravirtualizados**, agrupados bajo el estándar [virtIO](https://www.linux-kvm.org/page/Virtio). Estos dispositivos están diseñados específicamente para entornos virtualizados y ofrecen un **rendimiento cercano al de un hardware real**, reduciendo la sobrecarga en la CPU y mejorando el acceso a la red y al almacenamiento.  

El principal inconveniente de **virtIO** es que requiere soporte específico en el sistema operativo invitado. **Linux** incluye compatibilidad con virtIO de forma nativa, por lo que siempre es recomendable usarlo en entornos Linux. Sin embargo, otros sistemas operativos, como **Windows**, no incluyen estos controladores por defecto, por lo que es necesario instalarlos manualmente durante la instalación del sistema operativo.  

## libvirt

Aunque **QEMU/KVM** proporciona herramientas para gestionar máquinas virtuales, generalmente **no interactuamos directamente con ellas**. En su lugar, utilizamos **libvirt**, una capa de abstracción que facilita la gestión de entornos virtualizados.  

[**libvirt**](https://libvirt.org/) es una API de virtualización de código abierto que proporciona una interfaz unificada para controlar diferentes hipervisores. Incluye un demonio (`libvirtd`), una API estable y herramientas de gestión que simplifican la administración de máquinas virtuales. Está diseñada principalmente para entornos de virtualización nativos de Linux, como **KVM, LXC y Xen**, aunque también ofrece soporte para otros hipervisores como **VMware ESXi y Microsoft Hyper-V**.  

### Aplicaciones para gestionar libvirt

Al ser una API genérica, **libvirt** puede ser utilizada por [diversas aplicaciones](https://libvirt.org/apps.html), tanto de línea de comandos como interfaces gráficas y herramientas web. Algunas de las más destacadas son:

* **`virsh`**: Cliente de línea de comandos oficial de libvirt. Proporciona una shell interactiva para gestionar máquinas virtuales, redes y almacenamiento.  
* **Virt-Manager**: Interfaz gráfica que facilita la creación, configuración y administración de máquinas virtuales.  
* **`virtinst`**: Conjunto de herramientas que incluye `virt-install`, `virt-clone` y `virt-xml`, útiles para la instalación y clonación de máquinas virtuales.  
* **Virt-Viewer**: Aplicación que permite conectarse a la consola gráfica de una máquina virtual a través de protocolos como **SPICE o VNC**.  

En este curso utilizaremos **Virt-Manager** para gestionar los recursos virtualizados.

### Formato XML en libvirt

Cuando cualquier aplicación se conecta a **libvirt**, la información se intercambia en formato **XML**, lo que permite definir las máquinas virtuales de manera estructurada e interoperable. La documentación oficial de libvirt describe en detalle este formato en [XML Format](https://libvirt.org/format.html).  
