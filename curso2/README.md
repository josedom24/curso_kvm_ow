# Curso: Profundización a la virtualización con KVM/libvirt

1. Introducción a a la virtualización con KVM/libvirt
    * ¿Qué es la virtualización?
	* Tipos de virtualización
	* Introducción a QEMU/KVM y libvirt

2. Instalación de KVM/libvirt
    * Preparación del escenario de instalación
    * [Instalación de QEMU/KVM + libvirt](contenidos/unidad02/clase2.md)
    * [Recursos disponibles en QEMU/KVM + libvirt](contenidos/unidad02/clase3.md)

3. Creación de máquinas virtuales
    * [Creación de máquinas virtuales con virt-install](contenidos/unidad03/clase1.md)
    * [Características de las máquinas virtuales](contenidos/unidad03/clase2.md)
    * [Gestión de máquinas virtuales con virsh](contenidos/unidad03/clase3.md)
    * [Definición XML de una máquina virtual](contenidos/unidad03/clase4.md)
    * [Modificación de la definición de una máquina virtual](contenidos/unidad03/clase5.md)
    * [Acceso a la máquina virtual usando la consola serie](contenidos/unidad03/clase6.md)
    * [Creación de máquinas virtuales Windows con virt-install](contenidos/unidad03/clase7.md)
    * [Acceso a las máquinas virtuales desde el exterior](contenidos/unidad03/clase8.md)
    

4. Gestión del almacenamiento
    * [Introducción al almacenamiento en KVM/libvirt](contenidos/unidad04/clase1.md)
    * [Gestión de pools de almacenamiento](contenidos/unidad04/clase2.md)
    * [Gestión de volúmenes de almacenamiento con virsh](contenidos/unidad04/clase3.md)
    * [Gestión de volúmenes de almacenamiento con herramientas específicas](contenidos/unidad04/clase4.md)
    * [Trabajar con volúmenes en las máquinas virtuales](contenidos/unidad04/clase5.md)

5. Clonación e instantáneas de maquinas virtuales
    * Clonación de máquinas virtuales
    * Plantillas de máquinas virtuales
    * Clonación completa a partir de plantillas
    * Clonación enlazada a partir de plantillas
    * Instantáneas de máquinas virtuales
    * Backups de máquinas virtuales

6. Gestión de redes
    * Introducción a la gestión de redes
    * Definición de Redes Virtuales (Privadas)
    * Gestión de Redes Virtuales
    * Creación de un Puente Externo con Linux Bridge
    * Gestión de Redes Puentes (Públicas)
    * Configuración de red en las máquinas virtuales

7. Temas adicionales
    * Definición de un dominio con virsh
    * Despliegue automatizado de máquinas virtuales con virt-builder
    * Despliegue automatizado de máquinas virtuales usando cloud-init
    * Conexión local no privilegiada a libvirt
    * Conexión remota a libvirt
    * Migración de máquinas virtuales

8. Bibliografía
    * https://wiki.archlinux.org/title/Libvirt