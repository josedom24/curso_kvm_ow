# Ejemplo 1: Trabajando con redes puentes públicas

## Redes puente compartiendo la interfaz física del host

En este primer ejemplo, vamos a partir del siguiente escenario:

* No tenemos creado un puente externo `br0` como hemos visto anteriormente. Por lo tanto, la interfaz del host, que en este caso se llama `enp1s0` está conectada directamente a la red local del host. Para poder compartir una interfaz física usando un dispositivo macvtap no debe estar conectado aún puente externo.
* Hemos definido la red `red_macvtap`:

    ```
    usuario@kvm~$ virsh net-define red-macvtap.xml 
    usuario@kvm~$ virsh net-start red_macvtap 
    ```

* El host está conectado a una red con direccionamiento `192.168.100.0/24`, la puerta de enlace y el servidor DNS es la dirección `192.168.100.1`. Existe un servidor DHCP que configura de manera dinámica los equipos conectados a esta red.

Con la máquina parada, vamos a desconectar la interfaz de red a la red actual y vamos a conectarla a la red `red_macvtap`. Para hacer la operación de desconexión necesito la dirección MAC que obtengo con la primera instrucción:

```
usuario@kvm~$ virsh domiflist debian12
usuario@kvm~$ virsh detach-interface debian12 network --mac xx:xx:xx:xx:xx:xx --persistent 
usuario@kvm~$ virsh attach-interface debian12 network red_macvtap --model virtio --persistent
```

Arrancamos la máquina, y podemos comprobar el direccionamiento que ha tomado la máquina virtual, que estará conectada a la misma red del host. Existe una limitación en la implementación de macvtap: estas conexiones no permiten la comunicación directa entre el host (suponemos que su dirección IP es `192.168.100.127`) y la máquina virtual.

```
usuario@debian12:~$ ip a
...
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:eb:56:fd brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.250/24 brd 192.168.100.255 scope global dynamic enp1s0
...
usuario@debian12:~$ ping 192.168.100.127
PING 192.168.100.127 (192.168.100.127) 56(84) bytes of data.
From 192.168.100.250 icmp_seq=1 Destination Host Unreachable
...
```

## Redes puente conectadas a un bridge externo

En este caso partimos de que hemos configurado el puente externo `br0`, donde está conectado el host y vamos a conectar nuestra máquina virtual. Hacemos la misma operación, pero ahora conectamos la interface de red a la red `red_bridge` que corresponde a la red puente:

```
usuario@kvm~$ virsh detach-interface debian12 network --mac xx:xx:xx:xx:xx:xx --persistent 
usuario@kvm~$ virsh attach-interface debian12 network red_bridge --model virtio --persistent
```

Recuerda que también podemos hacer la conexión indicando al puente donde nos conectamos:

```
usuario@kvm~$ virsh attach-interface debian12 bridge br0 --model virtio --persistent
```

Accedemos a la máquina y comprobamos la configuración de la interface de red:

```
usuario@debian12:~$ ip a
...
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:eb:56:fd brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.250/24 brd 192.168.100.255 scope global dynamic enp1s0
...
```

Desde el host o desde cualquier otra máquina conectada a esta red, se podrá hacer una conexión a la máquina virtual sin ningún problema.

```
usuario@kvm~$ ping 192.168.100.250
PING 192.168.100.250 (192.168.100.250) 56(84) bytes of data.
64 bytes from 192.168.100.250: icmp_seq=1 ttl=64 time=0.418 ms
...
```