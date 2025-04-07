# Configuración de red en las máquinas virtuales

Todas las máquinas que hemos creado durante el curso se han conectado de forma predeterminada a la red `default`. 

Sin embargo, en este apartado vamos a aprender algunas cosas nuevas: a crear máquinas virtuales conectadas a otras redes definidas por el usuario y a añadir interfaces de red a máquinas virtuales ya existentes.

## Crear máquinas virtuales conectada a una red existente

Para crear una máquina virtual conectada, por ejemplo, a la red `red_nat`, podemos usar `virt-install`:

```
virt-install --connect qemu:///system \
			 --virt-type kvm \
			 --name debian12 \
			 --cdrom /var/lib/libvirt/images/debian-12.10.0-amd64-netinst.iso \
			 --os-variant debian12 \
			 --network network=red_nat \
			 --disk size=10 \
			 --memory 1024 \
			 --vcpus 1
```

* Con la opción `--network network=red_nat` indicamos que la máquina tendrá una interfaz de red conectada a la red cuyo nombre es `red_nat`.
* Para conectar una máquina a una red también podemos indicar el bridge virtual al que queremos conectarla. en este caso utilizaríamos la opción `--network bridge=virbr1`. `virbr1` es el bridge virtual que gestiona la red `red_nat`.
* Si indicamos varios parámetros `--network`, estaríamos añadiendo a la nueva máquina varias interfaces de red.

## Añadir nuevas interfaces de red a máquinas virtuales

Para añadir una nueva interfaz de red a una máquina virtual, vamos a modificar su definición XML. Podríamos usar `virsh edit` e incluir la definición XML de la nueva interfaz. Sin embargo, vamos a usar un comando de `virsh` que nos facilita la operación de añadir una nueva interfaz de red y por tanto, la modificación de la definición XML de la máquina. Es recomendable hacer esta operación con la máquina parada.

Por lo tanto, vamos a añadir a la máquina `debian12` una interfaz de red conectada a la red `red_nat`. Para ello, ejecutamos:

```
usuario@kvm~$ virsh attach-interface debian12 network red_nat --model virtio --persistent
```

Si la máquina virtual no tiene entorno gráfico y por tanto no tiene instalado el programa `NetworkManager` habrá que acceder a ella y configurar la nueva interfaz de red.

Podemos ver la configuración de las interfaces de red con el comando `virsh`:

```
usuario@kvm~$ virsh domifaddr debian12
```


Podríamos añadir una nueva interfaz de red indicando el puente virtual al que queremos realizar la conexión. En este caso tendríamos que ejecutar la misma instrucción pero el tipo de la conexión será `bridge`:

```
usuario@kvm~$ virsh attach-interface debian12 bridge virbr1 --model virtio --persistent
```

## Desconexión de interfaces de red

Por último indicar que si queremos desconectar una interfaz de red tenemos que indicar el tipo (`network` o `bridge`) y la dirección MAC:

```
usuario@kvm~$ virsh detach-interface debian12 bridge --mac 52:54:00:0c:06:2a --persistent 
```

