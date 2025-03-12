#  Tipos de virtualización

Según como funcione el Hipervisor podemos clasificar distintas técnicas de virtualización:

## Emulación

La emulación es una técnica en la que un software imita por completo una arquitectura de hardware, incluyendo el procesador, la memoria, los dispositivos de entrada/salida y el conjunto de instrucciones. De este modo, permite ejecutar programas o sistemas operativos diseñados para una arquitectura diferente sin modificaciones.  

Dado que la emulación requiere traducir cada instrucción y operación de hardware a nivel de software, su rendimiento suele ser **significativamente más bajo** en comparación con la virtualización, donde la CPU ejecuta directamente las instrucciones sin necesidad de traducción.  

* **Ejemplos**: QEMU, Microsoft Virtual PC, ...

## Virtualización completa o por hardware

La virtualización completa permite ejecutar un sistema operativo invitado **sin modificaciones**, simulando un entorno de hardware lo suficientemente completo para que el sistema operativo y sus aplicaciones se ejecuten como si estuvieran en una máquina física.  

Dependiendo del tipo de hipervisor utilizado, podemos hacer una subdivisión:  

* **Virtualización con hipervisores de tipo 1 (bare-metal)**:  
  En este caso, el hipervisor se ejecuta directamente sobre el hardware físico del host, sin un sistema operativo subyacente. Para esto, la CPU debe contar con extensiones de virtualización, como **Intel VT-x** o **AMD-V**. Este enfoque ofrece un **rendimiento cercano al de una máquina física**.  
  * **Ejemplos:** VMware ESXi, Microsoft Hyper-V, Xen, KVM (aunque se ejecuta dentro de Linux, convierte el kernel en un hipervisor).  

* **Virtualización con hipervisores de tipo 2 (hosteados)**:  
  Aquí, el hipervisor se ejecuta sobre un sistema operativo anfitrión, que gestiona el acceso al hardware. Este enfoque introduce una capa adicional que **reduce el rendimiento en comparación con los hipervisores de tipo 1**.  
  * **Ejemplos:** VMware Workstation, VirtualBox, Parallels Desktop, VMware Player.  


## Paravirtualización

En la paravirtualización, el hipervisor no emula completamente el hardware, sino que ofrece una **interfaz especial** a la que los sistemas operativos invitados deben adaptarse para comunicarse eficientemente con el hardware subyacente. Esto requiere modificar el kernel del sistema operativo invitado para utilizar llamadas específicas al hipervisor en lugar de acceder directamente al hardware.  

La paravirtualización ofrece **mayor rendimiento** que la virtualización completa tradicional sin soporte de hardware, ya que reduce la sobrecarga de emulación. Sin embargo, con la llegada de las extensiones de virtualización en CPU (Intel VT-x y AMD-V), la diferencia de rendimiento ha disminuido, y en muchos casos se prefiere la virtualización completa con dispositivos paravirtualizados (**VirtIO**).  

* **Ejemplos de paravirtualización pura:**  
    * **Xen (modo paravirtualizado, PV)**: Necesita un kernel modificado en la VM.  
    * **IBM z/VM**: Usa paravirtualización en entornos mainframe.  
* **Ejemplos de hipervisores con soporte de paravirtualización (vía drivers paravirtualizados):**  
    * **VMware ESXi** (usando VMware Tools para mejorar el rendimiento).  
    * **Microsoft Hyper-V** (con sus **Integration Services**).  
    * **KVM** (con VirtIO para almacenamiento, red y otros dispositivos).  

## Virtualización ligera

O también llamada **virtualización a nivel de sistema operativo**, o **virtualización basada en contenedores**. Es un método de virtualización en el que, sobre el núcleo del sistema operativo se ejecuta una capa de virtualización que permite que existan múltiples instancias aisladas de espacios de usuario. A cada espacio de usuario aislado lo llamamos **contenedor**. Por lo tanto, un contenedor es un conjunto de procesos aislados, que se ejecutan en un servidor, y que acceden a un sistema de ficheros propio, tienen una configuración red propio y accede a los recursos del host (memoria y CPU).
Podemos hacer la siguiente clasificación de contenedores:

* **Contenedores de Sistemas**: El uso que se hace de ellos es muy similar al que hacemos sobre una máquina virtual: se accede a ellos (normalmente por ssh), se instalan servicios, se actualizan, ejecutan un conjunto de procesos, ... 
    * **Ejemplo**: LXC(Linux Container).
* **Contenedores de Aplicación**: Se suelen usar para el despliegue de aplicaciones web. 
    * **Ejemplo**: Docker, Podman, ...



