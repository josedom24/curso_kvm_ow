# Modificación de la definición de una máquina virtual

La configuración de una máquina virtual en `libvirt` se define mediante un documento XML que describe todos sus atributos y recursos asignados. Este archivo especifica parámetros esenciales como el nombre, el UUID, la cantidad de memoria y vCPUs, los dispositivos de almacenamiento y red, la configuración de entrada/salida, el hipervisor utilizado y otras propiedades avanzadas. 

Podemos modificar esta configuración de dos maneras:  

1. **Edición manual del XML:** Usando `virsh edit <nombre_vm>`, que abre el archivo XML en un editor de texto. Este método permite realizar cualquier cambio, pero requiere cuidado para mantener una sintaxis válida. Una vez guardado, `libvirt` valida el archivo antes de aplicar los cambios.
2. **Uso de comandos específicos de `virsh`:** Permiten modificar parámetros individuales sin necesidad de editar directamente el XML, lo que reduce el riesgo de errores.  

Podemos realizar distintos tipos de cambios:

* **Cambios dinámicos (en caliente):** Aplicables sin detener la máquina virtual, como la asignación de vCPUs adicionales (si el hipervisor lo permite) o la conexión de discos y redes.  
* **Cambios persistentes:** Afectan la definición almacenada de la máquina virtual y permanecen tras un reinicio.  
* **Cambios que requieren apagado:** Algunas modificaciones estructurales, como la asignación de memoria fija o cambios en el tipo de bus de almacenamiento, requieren detener la máquina antes de aplicarse.  
* **Cambios que requieren reinicio:** Algunas modificaciones, como la actualización de ciertos parámetros de CPU o la modificación de controladores de dispositivos, solo se activan después de un reinicio de la máquina virtual.  
 
Veamos algunos ejemplos:

## Modificar el nombre de una máquina virtual

En este caso la modificación la vamos a realizar con el comando `virsh domrename`, que modificará internamente la definición XML. Este cambio requiere que la máquina esté parada.

```
usuario@kvm:~$ virsh domrename debian12 maquina_linux
```

## Modificar la asignación de vCPU

Suponemos que la máquina está parada. Comprobamos el número de vCPU asignadas a la máquina:

```
usuario@kvm:~$ virsh vcpucount maquina_linux
```

Podemos editar la configuración XML y cambiar el valor de la etiqueta `<vcpu>`:

```
usuario@kvm:~$ virsh edit maquina_linux
...
  <vcpu placement='static'>2</vcpu>
...
```

Y volvemos a comprobar la información de la máquina:

```
usuario@kvm:~$ virsh vcpucount maquina_linux
```

También podemos modificar este parámetro usando la siguiente instrucción que modifica directamente la definición XML:

```
usuario@kvm:~$ virsh setvcpus maquina_linux 4 --config
```

El parámetro `--config` modifica la configuración persistente de la máquina virtual para que el cambio se mantenga tras un reinicio.

Si la máquina está funcionando (**modificación en caliente**) podemos usar la instrucción:

```
usuario@kvm:~$ virsh setvcpus maquina_linux 2 --live --config
```

El parámetro `--live` aplica el cambio solo en la ejecución actual de la máquina virtual. Si indicamos el parámetro `--config` el cambio se mantendrá después del reinicio.


## Modificar la asignación de memoria RAM

Volvemos a suponer que la máquina está parada. Podemos editar la configuración XML y modificar las dos etiquetas relacionadas con la memoria:

* `<memory>`: Valor máximo de RAM que podemos asignar a la máquina "en caliente" (funcionando).
* `<currentMemory>`: Cantidad de memoria asignada a la máquina.

Por ejemplo, dejamos la asignación de memoria en un 1 Gb, y cambiamos la memoria máxima a 3 Gb:

```
usuario@kvm:~$ virsh edit maquina_linux
...
  <memory unit='KiB'>3145728</memory>
  <currentMemory unit='KiB'>1048576</currentMemory>
...
```

Podemos comprobar el cambio:

```
usuario@kvm:~$ virsh dominfo maquina_linux
...
Memoria máxima: 3145728 KiB
Memoria utilizada: 1048576 KiB
...
```

Ahora iniciamos la máquina y podemos cambiar "en caliente" la memoria de la máquina hasta un máximo de 3 Gb, para ello vamos a usar el comando `virsh setmem`.

```
usuario@kvm:~$ virsh start maquina_linux
usuario@kvm:~$ virsh setmem maquina_linux 2048M --live --config
usuario@kvm:~$ virsh dominfo maquina_linux
```

