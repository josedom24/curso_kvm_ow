# Ejemplo 1: Trabajando con redes puentes públicas

## Redes puente compartiendo la interfaz física del host

En este primer ejemplo, vamos a partir del siguiente escenario:

* No tenemos creado un puente externo `br0` como hemos visto anteriormente. Por lo tanto la interfaz del host, que en este caso se llama `enp1s0` está conectada directamente a la red local del host. Para poder compartir una interfaz física usando un dispositivo macvtap no debe estar conectado aun puente externo.
* Hemos definido la red `red_macvtap`.
* El host está conectado a un red con direccionamiento `192.168.100.0/24`, la puerta de enlace y el servidor DNS es la dirección `192.168.100.1`. Existe un servidor DHCP que configura de manera dinámica los equipos conectados a esta red.

Con la máquina parada, vamos a desconectar la interfaz de red a la red actual y vamos a conectarla a la red `red_macvtap`. Para hacer la operación de desconexión necesito la dirección MAC que obtengo con la primera instrucción:

```
usuario@kvm~$ virsh domifaddr debian12
usuario@kvm~$ virsh detach-interface debian12 bridge --mac 52:54:00:0c:06:2a --persistent 
usuario@kvm~$ virsh attach-interface debian12 network red_macvtap --model virtio --persistent
```

Arrancamos la máquina, nos vamos a la configuración de red y podemos comprobar el direccionamiento que ha tomado la máquina virtual, que estará conectada a la misma red del host. Existe una una limitación en la implementación de macvtap: estas conexiones no permiten la comunicación directa entre el host y la máquina virtual.

IP A

## Redes puente conectadas a un bridge externo

En este caso partimos de que hemos configurado el puente externo `br0`, donde está conectado el host y vamos a conectar nuestra máquina virtual. Hacemos la misma operación, pero ahora conectamos la interface de red a la red `red_bridge` que corresponde a la red puente:

```
usuario@kvm~$ virsh detach-interface debian12 bridge --mac 52:54:00:0c:06:2a --persistent 
usuario@kvm~$ virsh attach-interface debian12 network red_bridge --model virtio --persistent
```

Recuerda que también podemos hacer la conexión indicando al puente donde nos conectamos:

```
usuario@kvm~$ virsh attach-interface debian12 bridge br0 --model virtio --persistent
```

Accedemos a la máquina y comprobamos la configuración de la interface de red:

IP A

Desde el host o desde cualquier otra máquina conectada a esta red, se podrá hacer una conexión a la máquina virtual sin ningún problema.