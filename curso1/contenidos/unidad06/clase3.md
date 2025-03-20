# Definición de redes virtuales aisladas y muy aisladas

Las Redes Virtuales aisladas (Isolated) es un tipo de Red Virtual Privada, donde las máquinas virtuales tomas direccionamiento privado. No tenemos un mecanismo de router/nat, por lo que las máquinas virtuales no tienen conectividad con el exterior. 

Si el anfitrión está conectada a la red la llamaos **Red Aislada**, en caso contrario la llamamos **Red muy Aislada**.

## Creación de una red virtual aislada

Pulsamos sobre el botón de añadir una nueva red, indicamos el **Nombre**, en el **Modo** elegimos **Isolado** e indicamos su configuración:

* **Configuración IPv4**: 
    * Elegimos la opción de **Habilitar IPv4**, indicando la dirección de **Red** con la que vamos a trabajar. La primera dirección será la dirección IP del anfitrión en esta red y la puerta de enlace de las máquinas virtuales conectadas a esta red.
    * En este caso y según nuestras necesidades podemos habilitar o no el servidor DHCP.
* **Configuración IPv6**: Si queremos trabajar con IPv6 podemos seleccionar la opción **Habilitar IPv6**.
* **Nombre de dominio DNS**: Esta opción permiten definir cómo se asignará el dominio a las máquinas virtuales conectadas a esa red, funciona de la misma manera que en las redes de tipo NAT.
    
IMAGEN

De la misma manera que en las redes de tipo NAT, podemos comprobar que al crear la red estará iniciada y se habrá escogido la opción de **Auotoiniciar**. Además se habrá creado otro bridge para gestionar esta red.

IMAGEN

También podemos ver la definición XML de la red:

IMAGEN

## Creación de una red virtual muy aislada

Para crear una Red Virtual muy Aislada, seguimos el mismo procedimiento que la anterior, pero en este caso, no elegimos la opción de **Habilitar IPv4**. Por lo tanto se creará un nuevo bridge donde se conectarán las máquinas virtuales, pero el anfitrión no estará conectado a esta red.

IMAGEN

Al terminar la creación, la red estará iniciada y se habrá configurado la opción de **Auotiniciar**. Además podremos ver el nombre del bridge que se ha creado.

IMAGEN

Por último podremos ver la definición XML de esta red.

IMAGEN