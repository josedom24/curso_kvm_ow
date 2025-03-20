# Gestión de redes puentes

Con virt-manager no podemos crear Redes Puentes como veíamos anteriormente con las redes privadas. Sin embargo, en la configuración de las tarjetas de red de las máquinas virtuales, podemos indicar que no se conecten una red privada, configurado el puente externo al que estará conectada o indicando el dispositivo físico que se va a utilizar 

## Creación de una máquina virtual conectado al puente externo br0

Iniciamos el asistente para crear una nueva máquina virtual, y llegamos a la última pantalla que llamamos **Resumen y selección de red**, donde nos encontramos el resumen de las características de la máquina que vamos a crear y nos permiten realizar la configuración de red.

En el apartado **Selección de Red** elegimos la opción **Dispositivo de puente...** y en la casilla **Nombre del dispositivo** indicamos el nombre del puente externo, en nuestro caso **br0**.

IMAGEN

## Modificación de la configuración de la interfaz de red

Si ya tenemos una máquina virtual funcionando, podemos cambiar la configuración de su interfaz de red. Para ello accedemos a la vista **Detalles**, escogemos la interfaz de red que queremos modificar y en el apartado **Fuente de red** es cogemos la opción **Dispositivo de puente...** y en la casilla **Nombre del dispositivo** indicamos el nombre del puente externo, en nuestro caso **br0**.

IMAGEN

En el próximo apartado aprenderemos a añadir nuevas interfaces de red a las máquinas virtuales y evidentemente en esa operación podremos configurar la nueva interfaz para que se conecte a un puente externo.

## Creación de una máquina virtual conectada al exterior compartiendo la interfaz física del host

En este caso 