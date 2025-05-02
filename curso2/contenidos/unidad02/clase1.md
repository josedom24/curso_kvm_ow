# Preparación del escenario de instalación

Para un entorno de virtualización con QEMU/KVM en producción, se recomienda ejecutarlo sobre una máquina física con recursos suficientes. Sin embargo, también es posible configurar un laboratorio en un entorno virtualizado, habilitando **virtualización anidada**.

## Verificación del soporte de virtualización en el hardware

Antes de proceder con la instalación, es imprescindible confirmar que la CPU admite extensiones de virtualización por hardware (**Intel VT-x** o **AMD-V**). Para ello, ejecutamos el siguiente comando como `root`:

```bash
egrep 'svm|vmx' /proc/cpuinfo --color
```

Si el resultado muestra varias líneas con los términos **vmx** (Intel) o **svm** (AMD), significa que la CPU soporta virtualización por hardware.

**Nota:** En equipos con BIOS/UEFI, es necesario habilitar la virtualización en la configuración del firmware. Si esta opción está desactivada, KVM no funcionará.



## Virtualización anidada

La **virtualización anidada** permite ejecutar hipervisores dentro de máquinas virtuales, facilitando la creación de entornos de prueba para KVM sin necesidad de hardware dedicado. Esto es útil para desarrollar, probar configuraciones o impartir cursos de virtualización.

### Virtualización anidada en VirtualBox

Para activar la virtualización anidada en **VirtualBox**, siga estos pasos:

1. En la configuración de la máquina virtual, diríjase a **Sistema** > **Procesador**.
2. Marque la opción **Habilitar VT-x/AMD-V**.
3. Active la opción **Virtualización anidada** si está disponible.
4. Para mejorar el rendimiento de la memoria, en **Sistema** > **Aceleración**, habilite **PAE/NX** y **IO APIC**.

### Virtualización anidada en KVM/QEMU

Normalmente, la virtualización anidada está activada, pero en la creación de la máquina virtual es necesario especificar que use la CPU del host. Esto es fundamental para que la máquina virtual pueda acceder a las extensiones de virtualización de la CPU del host de forma correcta, aprovechando la virtualización anidada.

Al crear una máquina virtual en KVM, especialmente si se planea habilitar virtualización anidada, es recomendable utilizar el modelo de CPU de tipo **host-passthrough**.

## Requisitos mínimos para el entorno del curso

Para un laboratorio de virtualización, se recomienda la siguiente configuración mínima:

| Recurso       | Recomendación |
|--------------|--------------|
| **RAM**     | 8 GB o más |
| **Almacenamiento** | 100 GB de espacio en disco |
| **CPU**     | 4 núcleos con virtualización por hardware |

### Consideraciones adicionales:

* **Memoria RAM:** Dependiendo del sistema operativo invitado:
  * Linux sin entorno gráfico: 512 MB - 1 GB.
  * Linux con GUI: 2 GB o más.
  * Windows: 2 GB como mínimo, idealmente 4 GB o más.
* **CPU:** Mayor cantidad de núcleos mejorará la capacidad de virtualización simultánea.
* **Almacenamiento:** Considerar espacio extra para ISO de instalación y discos virtuales.

Con esta configuración, es posible ejecutar múltiples máquinas virtuales sin comprometer el rendimiento del host.

