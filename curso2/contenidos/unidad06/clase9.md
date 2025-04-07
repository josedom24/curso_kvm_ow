# Ejemplo 3: Configuración de un router/NAT

Partiendo del último ejemplo del apartado anterior, queremos configurar la máquina Linux para que que funcione como router y realice NAT para que la máquina Windows que está conectada a una red interna tenga conectividad con el exterior. El esquema sería el siguiente:

![router](img/router.png)

Para ello tenemos que realizar los siguientes pasos:

1. Añadimos a la máquina Linux una nueva interfaz de red que le permita el acceso al exterior, en nuestro caso la vamos a conectar a la red `red-nat`:

    ```
    usuario@kvm~$ virsh attach-interface debian12 network red_nat --model virtio --persistent
    ```

    Realizamos la configuración de la nueva interface de red en el fichero `/etc/network/interfaces`:

    CONFIGURACIÓN DE RED

    Y comprobamos el direccionamiento de la máquina Linux:

    IP A

    Está máquina accede al exterior por la interfaz conectada a `red-nat`, en el ejemplo `enp7s0`.

2. Completamos la configuración de la máquina Windows. Configuramos su puerta de enlace que será la máquina Linux, es decir la dirección `172.22.0.2` e indicamos un servidor DNS.
 
 ![ejemplo3](img/ejemplo3_3.png)

3. Configuramos en la máquina Linux el **IP forwarding** que permite reenviar paquetes entre interfaces de red. Para hacerlo permanente, editamos el archivo `/etc/sysctl.conf` y descomentamos la línea:

```
#net.ipv4.ip_forward=1
```

A continuación ejecutamos la siguiente instrucción para  aplica los cambios:

```
usuario@debian:~$ sudo sysctl -p
```

4. Configuramos `iptables` para realizar NAT. Además queremos que las reglas de cortafuegos que configuremos sean permanentes. Para  ello, instalamos los paquetes necesarios:

```
usuario@debian:~$ sudo apt install iptables iptables-persistent
```

A continuación creamos la regla de NAT con iptables:

```
usuario@debian:~$ sudo iptables -t nat -A POSTROUTING -o enp7s0 -s 172.22.0.0/16 -j MASQUERADE
```

Esta regla realiza las siguientes acciones:

* Todos los paquetes que provienen de la red muy aislada (`-s 172.22.0.0/16`).
* Y salgan al exterior (`-o enp7s0`) (recuerda que debes cambiar la interfaz si en tu caso se llama de manera distinta).
* Se enmascara, es decir se cambia la dirección de origen por la del router (la máquina Linux) para que pueda enrutarse al exterior.

Para que la regla sea permanente después de un reinicio, la guardamos con la siguiente instrucción:

```
usuario@debian:~$ sudo iptables-save > /etc/iptables/rules.v4
```

5. Finalmente podemos comprobar que la máquina Windows ya tiene conectividad con el exterior y puede resolver nombres por el DNS:

    ![ejemplo3](img/ejemplo3_4.png)