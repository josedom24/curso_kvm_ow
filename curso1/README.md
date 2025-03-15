# Curso: Introducción a la virtualización con KVM/libvirt usando virt-manager

1. Introducción a a la virtualización con KVM/libvirt
    * [¿Qué es la virtualización?](contenidos/unidad01/clase1.md)
	* [Tipos de virtualización](contenidos/unidad01/clase2.md)
	* [Introducción a QEMU/KVM y libvirt](contenidos/unidad01/clase3.md)

2. Introducción a virt-manager
    * [Preparación del escenario de instalación](contenidos/unidad02/clase1.md)
    * [Instalación de virt-manager](contenidos/unidad02/clase2.md)
    * [Tipos de conexiones a libvirt](contenidos/unidad02/clase3.md)
    * [Recursos disponibles en virt-manager](contenidos/unidad02/clase4.md)

3. Creación de máquinas virtuales con virt-manager
    * [Creación de máquinas virtuales Linux](contenidos/unidad03/clase1.md)
    * [Características de las máquinas virtuales](contenidos/unidad03/clase2.md)
    * [Gestión de máquinas virtuales](contenidos/unidad03/clase3.md)
    * [Detalles de las máquinas virtuales](contenidos/unidad03/clase4.md)
    * Creación de máquinas virtuales Windows
    * Acceso a las máquinas virtuales desde el exterior

4. Gestión del almacenamiento en virt-manager
    * Introducción al almacenamiento
    * Introducción al almacenamiento en virt-manager
    * Gestión de Pools de almacenamiento
    * Gestión de volúmenes de almacenamiento
    * Trabajar con volúmenes en las máquinas virtuales

5. Clonación e instantáneas de maquinas virtuales
    * Clonación de máquinas virtuales
    * Plantillas de máquinas virtuales
    * Clonación completa a partir de plantillas
    * Clonación enlazada a partir de plantillas
    * Instantáneas de máquinas virtuales

6. Gestión de redes en virt-manager
    * Introducción a la gestión de redes
    * Definición de Redes Virtuales (Privadas)
    * Gestión de Redes Virtuales
    * Creación de un Puente Externo con Linux Bridge
    * Gestión de Redes Puentes (Públicas)
    * Configuración de red en las máquinas virtuales

7. Temas adicionales
    * Acceso a la máquina virtual usando la consola serie (sudo systemctl start getty@ttyS0)
    * Conexión local no privilegiada a libvirt
    * Conexión remota a libvirt
    * Migración de máquinas virtuales
    * Gestión de máquinas virtuales con cockpit
