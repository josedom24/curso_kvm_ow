# ¿Qué es la virtualización?

Según la Wikipedia: *La virtualización utiliza el software para imitar las características del hardware y crear un sistema informático virtual*.

Esto nos permite ejecutar más de un sistema virtual, y múltiples sistemas operativos y aplicaciones, en un solo servidor, aumentando el rendimiento del hardware disponible e incrementando el tiempo de procesamiento de un equipo, ya que habitualmente se desaprovecha gran parte.

## ¿Para qué se utiliza la virtualización?

La virtualización tiene múltiples aplicaciones en distintos entornos, desde la administración de sistemas hasta la investigación y el aprendizaje. Algunos de sus usos más comunes incluyen:  

* **Aislamiento e independencia de servicios y contenidos**: Permite ejecutar diferentes aplicaciones y sistemas operativos en entornos independientes, evitando conflictos entre ellos y aumentando la seguridad.  
* **Laboratorio de pruebas**: Facilita la experimentación con nuevas configuraciones, software o sistemas operativos sin afectar el entorno de producción.  
* **Virtualización de arquitecturas de las que no se dispone**: Permite emular arquitecturas distintas (por ejemplo, ejecutar un sistema ARM en un equipo x86 con QEMU).  
* **Creación de clústeres y sistemas distribuidos**: Se pueden desplegar múltiples máquinas virtuales en un mismo host o en una infraestructura distribuida para realizar pruebas de escalabilidad, alta disponibilidad o balanceo de carga.  
* **Herramienta de aprendizaje**: Es ideal para la enseñanza de administración de sistemas, redes y seguridad informática sin necesidad de contar con hardware físico adicional.  
* **Consolidación de servidores**: Reduce costos y consumo energético al ejecutar múltiples servidores virtuales en un mismo hardware físico.  
* **Recuperación ante desastres y alta disponibilidad**: Facilita la copia de seguridad y restauración rápida de sistemas en caso de fallos.  
* **Ejecutar software heredado**: Permite mantener aplicaciones antiguas en entornos compatibles sin necesidad de hardware obsoleto.  

## Ventajas e inconvenientes de la virtualización

Las principales ventajas que podemos indicar serían:

* **Ahorro económico**: Permite reducir la cantidad de hardware necesario, lo que disminuye los costos de adquisición, mantenimiento y operación.  
* **Mayor seguridad**: Facilita el aislamiento de servicios, lo que ayuda a evitar que fallos o vulnerabilidades en un sistema afecten a otros. También permite realizar copias de seguridad y restauraciones de manera sencilla.  
* **Mejor aprovechamiento de recursos**: La virtualización permite utilizar de manera más eficiente la CPU, memoria y almacenamiento, consolidando varias máquinas virtuales en un solo hardware físico.  
* **Migración en vivo**: Tecnologías como **Live Migration** en KVM o **vMotion** en VMware permiten mover máquinas virtuales de un servidor físico a otro sin interrupciones, facilitando el mantenimiento y la escalabilidad.  
* **Ahorro energético**: Al reducir la cantidad de servidores físicos en funcionamiento, se disminuye el consumo energético y la necesidad de refrigeración.  
* **Flexibilidad y escalabilidad**: Se pueden crear, modificar o eliminar máquinas virtuales según las necesidades sin realizar cambios en el hardware físico.  

Como desventajas podríamos señalar:

* **Dependencia de un solo equipo físico**: Si el hardware físico falla, puede afectar a todas las máquinas virtuales alojadas en él, a menos que se dispongan de sistemas de alta disponibilidad.  
* **Penalización en rendimiento**: Aunque el rendimiento de la virtualización ha mejorado gracias a las extensiones de hardware (Intel VT-x, AMD-V), sigue existiendo una pequeña sobrecarga en comparación con el rendimiento nativo.  
* **Complejidad en la configuración y gestión**: Implementar y administrar un entorno virtualizado requiere conocimientos avanzados sobre hipervisores, redes virtuales y almacenamiento.  

## Conceptos de virtualización

* **host o anfitrión**: Es el sistema operativo que ejecuta el software de virtualización. El anfitrión controla el hardware real.
* **guest, invitado o huésped**: Es el sistema operativo virtualizado.
* **Hipervisor**: El software de virtualización.

