# Gestión de redes virtuales privadas

En este apartado vamos  a estudiar como trabajar con las redes virtuales con `virsh`.

Podemos ver las redes que tenemos definidas ejecutando:

```
usuario@kvm~$ virsh net-list --all
```

Utilizamos la opción `--all` para listar las redes iniciadas y paradas.

## Creación de redes

Las redes se crean a partir de su definición XML que tenemos guardado en un fichero. En este caso tenemos el fichero `red-nat.xml`, donde tenemos la definición de una red virtual de tipo NAT.

Para crear la nueva red, ejecutamos:

```
usuario@kvm~$ virsh net-define red-nat.xml
```

Si utilizamos el comando `virsh net-create` estaríamos creando la red de forma temporal, no persistente.

## Gestión de redes

La red no se puede utilizar hasta que no se inicie, para ello:

```
usuario@kvm~$ virsh net-start red_nat
```

Si vamos a usar esta red con mucha frecuencia es recomendable activar la propiedad de autoiniciar para que se inicie de forma automática al iniciar el host. Para ello:

```
usuario@kvm~$ virsh net-autostart red_nat
```

Podemos obtener información de la red ejecutando:

```
usuario@kvm~$ virsh net-info red_nat
```

Al iniciar podemos comprobar que se ha creado el bridge virtual y una nueva interfaz de red en el host. Para ello instalamos el paquete `bridge-utils` para poder usar el comando `brctl`:

```
usuario@kvm~$ sudo brctl show
bridge name	bridge id		STP enabled	interfaces
virbr0		8000.525400aea33d	yes		
virbr1		8000.5254002daec2	yes	
```

En el host, el bridge virtual aparece como una interfaz de red:

```
usuario@kvm~$ ip a
...
4: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:ae:a3:3d brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
5: virbr1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:2d:ae:c2 brd ff:ff:ff:ff:ff:ff
    inet 192.168.101.1/24 brd 192.168.101.255 scope global virbr1
       valid_lft forever preferred_lft forever
```

Podemos considerar que la interfaz de red del bridge virtual corresponde a la conexión del host con el bridge.

Para ver la definición XML de la red que hemos creado, ejecutamos:

```
usuario@kvm~$ virsh net-dumpxml red_nat
```

De forma similar podemos crear la red aislada y la muy aislada cuyas definiciones creamos en el apartado anterior. Finalmente, tendríamos varios puentes creados en el host:
```
usuario@kvm~$ sudo brctl show
bridge name	bridge id		STP enabled	interfaces
virbr0		8000.525400aea33d	yes		
virbr1		8000.5254002daec2	yes		
virbr2		8000.525400d51f31	yes
virbr3		8000.525400f1d203	yes
```

Finalmente, indicar que para parar una red utilizamos el comando `virsh net-stop` y para eliminarla el comando `virsh net-undefined`.

## Consulta de las concesiones DHCP

Si tenemos una máquina virtual que ha tomado una configuración dinámica a partir del servidor DHCP de una red privada, podemos obtener información de las concesiones de ese servidor DHCP ejecutando la siguiente instrucción:

```
usuario@kvm~$ virsh net-dhcp-leases default
 Expiry Time           MAC address         Protocol   IP address           Hostname             Client ID or DUID
----------------------------------------------------------------------------------------------------------------------------------------------------------
 2025-xx-xx 19:16:33   52:54:00:13:88:94   ipv4       192.168.122.223/24   plantilla-debian12   ff:00:13:88:94:00:01:00:01:2f:99:28:12:52:54:00:00:66:be
```

