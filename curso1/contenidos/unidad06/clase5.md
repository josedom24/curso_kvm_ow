# Ejemplo 1: Trabajando con redes virtuales privadas

En este apartado vamos a configurar las dos máquinas que hemos creados con distintas configuraciones de red utilizando las redes virtuales que hemos creado.

## Trabajando con la red de tipo NAT

En primer lugar vamos a cambiar la configuración de la máquina Linux para conectarla a la red de tipo NAT llamad `red-nat` que hemos creado anteriormente (recordamos que el direccionamiento que pusimos para esta red es `192.168.101.0/24`). Para ello, modificamos la configuración del dispositivo de red de la máquina (recuerda que este cambio no se puede hacer con la máquina funcionando, si lo hacemos tendrá efecto tras un reinicio):

![ejemplo](img/ejemplo1_1.png)

Accedemos a la máquina virtual y comprobamos el direccionamiento que ha tomado:

![ejemplo](img/ejemplo1_2.png)

También podemos comprobar que tenemos acceso a internet sin ningún problema.

## Trabajando con la red aislada

A continuación vamos a conectar las dos máquinas a la red aislada llamada `red-aislada`, recordamos que habíamos deshabilitado el servidor DHCP y que en este caso comprobaremos que el host no hace la función de router con el mecanismo de NAT. Para ello vamos a realizar las siguientes configuraciones:

* Vamos a modificar la configuración de la máquina Linux para conectarla a la red aislada, y posteriormente configuramos la interfaz de red de forma manual con el direccionamiento que habíamos configurado en la red `red-aislada` que era `192.168.102.0/24`, tenemos que recordar que en esta red está conectada el host con la dirección `192.168.102.1`.

    ![ejemplo](img/ejemplo1_3.png)

    ![ejemplo](img/ejemplo1_4.png)

* Realizamos la misma operación en la máquina Windows. Además deshabilitamos el cortafuegos para que nos permita hacer las posteriores comprobaciones:

    ![ejemplo](img/ejemplo1_5.png)
    
    ![ejemplo](img/ejemplo1_6.png)

    ![ejemplo](img/ejemplo1_7.png)

A continuación, arrancamos las máquinas y vamos a realizar algunas comprobaciones:

* Desde la máquina Linux tenemos conectividad con la máquina Windows y con el host, pero no tenemos conectividad con el exterior:

    ![ejemplo](img/ejemplo1_8.png)

* Del mismo desde la máquina windows tenemos conectividad con la máquina Linux t el host, pero tampoco con el exterior:

    ![ejemplo](img/ejemplo1_9.png)

