# Definición de redes virtuales privadas

Las redes que gestiona libvirt se definen con el formato XML. Puedes profundizar en el formato XML con los que se definen las redes consultando el documento [Network XML format](https://libvirt.org/formatnetwork.html). 

## Definición de redes virtuales de tipo NAT

La red `default` con la que hemos trabajado es de este tipo. La configuración de la red `default` la podemos encontrar en el fichero `/usr/share/libvirt/networks/default.xml`. A partir de ese fichero podemos crear la definición de otra red de tipo NAT, por ejemplo guardamos en el fichero `red-nat.xml` el siguiente contenido:

```xml
<network>
  <name>red_nat</name>
  <bridge name='virbr1'/>
  <forward/>
  <ip address='192.168.101.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.101.2' end='192.168.101.254'/>
    </dhcp>
  </ip>
</network>
```

Veamos las etiquetas:

* `<name>`: Nombre de la red.
* `<bridge>`: Indicamos el nombre del bridge virtual que se va a utilizar.
* `<forward>`: Indica que las máquinas virtuales van a tener conectividad con el exterior. Por defecto, si no se indicaba nada, el tipo es **nat**: `<forward mode="nat"/>`. El modo también puede ser:
	* `router`: Las redes tipo [router](https://wiki.libvirt.org/page/VirtualNetworking#Routed_mode) también dan acceso a las máquinas virtuales al exterior, pero en ese caso no se utiliza el mecanismo de NAT, sino que se usan rutas de encaminamiento en el host.
	* `open`: Similar a la anterior, excepto que no se añaden reglas de firewall para asegurar que cualquier tráfico sea aceptado. 
* `<IP>`: Donde se indica la dirección IP y la máscara de red de la dirección del host en la red. Es decir, el host está conectado al bridge virtual con esa dirección.
	* `<dhcp>`: **Este elemento es optativo**. Si queremos tener un servidor DHCP configurado en el host, lo configuramos en esta etiqueta, por ejemplo poniendo el rango en la etiqueta `<range>`. 
	
## Definición de redes virtuales aisladas

La definición de una Red virtual aislada es igual a la de tipo NAT, pero quitando la etiqueta `<forward>` para deshabilitar la característica de que el host haga router/nat. La definición la guardamos en el fichero `red-aislada.xml`, y podría quedar de este modo:

```xml
<network>
  <name>red_aislada</name>
  <bridge name='virbr2'/>
  <ip address='192.168.102.1' netmask='255.255.255.0'/>
</network>
```

En este caso hemos quitado la etiqueta `<dhcp>` por lo que no tendríamos un servidor DHCP para configurar de forma dinámica las interfaces de red de las máquinas virtuales.

## Definición de redes virtuales muy aisladas
 
Son similares a la anterior, pero el host no se conecta a la red. Por lo tanto, no tenemos ni servidor DNS, ni DHCP. Al crear este tipo de red, simplemente se creará un bridge virtual donde se conectarán las máquinas virtuales. Tendremos que configurar de forma estática su direccionamiento. Por lo tanto, la definición la guardamos en el fichero `red-muy-aislada.xml`:

```xml
<network>
  <name>red_muy_aislada</name>
  <bridge name='virbr3'/>
</network>
```

