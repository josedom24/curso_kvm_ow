# Tipos de virtualización

Según el funcionamiento del hipervisor, podemos clasificar diferentes técnicas de virtualización. A continuación se profundiza en cada una de ellas, abordando tanto sus mecanismos internos como sus implicaciones de rendimiento y uso en entornos avanzados.

## Emulación

La **emulación** es una técnica en la que un software replica por completo la arquitectura de hardware de una plataforma. Esto implica la simulación de los componentes esenciales, como el procesador, la memoria, los dispositivos de entrada/salida y el conjunto de instrucciones, permitiendo ejecutar sistemas operativos y aplicaciones diseñados para una plataforma diferente sin modificaciones.

En términos técnicos, la emulación lleva a cabo una traducción **a nivel de instrucción** entre la arquitectura del sistema huésped y la del sistema emulado. Esta traducción es costosa en términos de rendimiento, ya que cada instrucción debe ser interpretada y transformada, lo que introduce **latencia significativa**.

El uso de la emulación es común cuando se necesita compatibilidad entre plataformas heterogéneas, pero su rendimiento suele ser **sustancialmente inferior** en comparación con otras técnicas de virtualización.

* **Ejemplos**: QEMU (emula diferentes arquitecturas, como ARM sobre x86), Microsoft Virtual PC (para emular sistemas Windows en hardware diferente).

## Virtualización completa o por hardware

La **virtualización completa** simula un entorno de hardware completo para que los sistemas operativos invitados puedan ejecutarse sin modificaciones. Se distingue de la emulación en que no requiere traducción de instrucciones a nivel de software, sino que puede ejecutar código de invitado **de forma nativa**, aprovechando las capacidades de la CPU para gestionar la ejecución.

Dependiendo del tipo de hipervisor utilizado, la virtualización completa se subdivide en dos enfoques:

### Virtualización con hipervisores de tipo 1 (bare-metal)

Los hipervisores de tipo 1, también conocidos como **bare-metal**, se ejecutan directamente sobre el hardware físico sin un sistema operativo subyacente. Este tipo de hipervisor gestiona completamente el hardware y las máquinas virtuales, lo que proporciona un **rendimiento cercano al nativo**. Además, debido a la interacción directa con el hardware, los hipervisores tipo 1 suelen ofrecer mejor escalabilidad, mayor fiabilidad y menores latencias en comparación con los hipervisores de tipo 2.

Las extensiones de virtualización en la CPU, como **Intel VT-x** o **AMD-V**, son fundamentales para este enfoque, ya que permiten al hipervisor utilizar funcionalidades específicas del procesador para facilitar la virtualización y mejorar el rendimiento.

* **Ejemplos**: VMware ESXi, Microsoft Hyper-V, Xen (en modo completo), KVM (aunque es un hipervisor basado en Linux, se considera tipo 1 por su integración directa con el kernel de Linux como hipervisor).

### Virtualización con hipervisores de tipo 2 (hosteados)

Los hipervisores de tipo 2 se ejecutan sobre un sistema operativo anfitrión, el cual a su vez gestiona el acceso al hardware. Aunque este enfoque permite una mayor flexibilidad al no requerir modificaciones en el hardware, introduce una **capa adicional de software** que impacta en el rendimiento. En general, los hipervisores de tipo 2 son menos eficientes en términos de uso de recursos y latencia, ya que dependen de un sistema operativo huésped para la gestión de los recursos.

A pesar de esto, los hipervisores de tipo 2 suelen ser más adecuados para entornos de desarrollo o pruebas, donde la flexibilidad y la facilidad de uso son más importantes que la máxima eficiencia.

* **Ejemplos**: VMware Workstation, VirtualBox, Parallels Desktop, VMware Player.

## Paravirtualización

La **paravirtualización** es un enfoque intermedio entre la emulación y la virtualización completa. En este caso, el sistema operativo invitado es consciente de que está siendo virtualizado y, en lugar de hacer llamadas directas al hardware, utiliza una interfaz de comunicación específica proporcionada por el hipervisor. Esto mejora la eficiencia al reducir la sobrecarga de emulación, ya que se minimiza la necesidad de traducir instrucciones de hardware.

En la paravirtualización, es necesario **modificar el kernel** del sistema operativo invitado para que sea compatible con el hipervisor. Aunque las extensiones de virtualización en la CPU han reducido las diferencias de rendimiento, la paravirtualización sigue siendo útil en entornos donde la eficiencia y la capacidad de control del hipervisor son prioritarias.

* **Ejemplos de paravirtualización pura**:  
  * **Xen (modo paravirtualizado, PV)**: Xen ofrece tanto un modo de virtualización completa como un modo paravirtualizado (PV), donde se requiere que el sistema operativo invitado se adapte para comunicarse directamente con el hipervisor.  
  * **IBM z/VM**: Utiliza paravirtualización para la virtualización de mainframes, optimizando el rendimiento a través de la colaboración entre el hipervisor y los sistemas invitados.

* **Ejemplos de hipervisores con soporte de paravirtualización (drivers paravirtualizados)**:  
  * **VMware ESXi** (utiliza VMware Tools para mejorar la eficiencia del sistema invitado).  
  * **Microsoft Hyper-V** (provee **Integration Services** para optimizar la comunicación entre el hipervisor y los sistemas invitados).  
  * **KVM** (implementa **VirtIO** para paravirtualización de dispositivos como almacenamiento, red, etc.).

## Virtualización ligera

La **virtualización ligera**, también conocida como **virtualización a nivel de sistema operativo** o **contenedorización**, no se basa en simular un hardware completo, sino en **aislar procesos** dentro del mismo sistema operativo. Este enfoque permite ejecutar múltiples instancias de aplicaciones o servicios de manera aislada, pero sin la sobrecarga de una máquina virtual completa. Cada instancia (contenedor) comparte el mismo núcleo del sistema operativo subyacente, pero tiene su propio sistema de archivos, red y otros recursos virtualizados.

Este tipo de virtualización es **muy eficiente** en términos de uso de recursos, ya que no requiere duplicar el sistema operativo para cada instancia. Además, los contenedores pueden iniciarse rápidamente, lo que los hace ideales para entornos de **microservicios** y despliegues en la nube.

Dentro de la virtualización basada en contenedores, podemos distinguir dos tipos principales:

* **Contenedores de Sistema**: Estos contenedores permiten ejecutar instancias aisladas de un sistema operativo completo. Aunque comparten el mismo núcleo, cada contenedor se comporta como una máquina virtual ligera, con su propio espacio de usuario, red y almacenamiento.
  * **Ejemplo**: **LXC (Linux Containers)**, que proporciona una virtualización completa del sistema operativo sin la necesidad de modificar el kernel.

* **Contenedores de Aplicación**: Son más ligeros que los contenedores de sistema y están diseñados específicamente para ejecutar aplicaciones de manera aislada, utilizando recursos del sistema operativo subyacente sin necesidad de virtualizar el sistema operativo completo.
  * **Ejemplos**: **Docker**, **Podman**, **Kubernetes** (orquestador de contenedores).
