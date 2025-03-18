# Creación de máquinas virtuales Windows (2ª parte)

Una vez que hemos realizado la instalación del sistema operativo, podemos comprobar que la máquina no tiene acceso a internet, ya que tenemos que cargar los drivers de la tarjeta de red VirtIO que hemos configurado:

## Configuración de la red

Como indicábamos anteriormente, también hemos escogido el controlador VirtIO para la tarjeta de red. Una vez realizada la configuración tendremos que instalar los drivers adecuados para que funcione la tarjeta de red. Para ello, actualizamos el controlador del dispositivo **Controladora Ethernet** en el **Administrador de dispositivos**:

![virt-manager](img/windows10.png)

Y escogemos la unidad del CDROM donde hemos montado los drivers VirtIO y elegimos la opción **Incluir subcarpetas** para que la busque de manera recursiva (los drivers de la tarjeta de red se encuentra en la carpeta `NetKVM\<carpeta con el nombre de tu versión de windows>\amd64`):

![virt-manager](img/windows11.png)

## Configuración de otros elementos hardware de la máquina

Usando el mismo procedimiento configuramos los drivers de los elementos hardware que no están dando alguna indicación de error en la configuración:

* Controladora simple de comunicaciones PCI
* Dispositivo PCI

## Características de las máquina Windows que hemos creado

Podemos comprobar que nuestra máquina se ha conectado a la red privada de tipo NAT llamada `default`, recibiendo una dirección IP dinámica con la siguiente configuración. Para ver la configuración: **Configuración - Red e Internet - Estado - Propiedades**:

![virt-manager](img/windows12.png)

Además podemos comprobar que tenemos acceso a internet.

También podemos comprobar el modelo del procesador, el tamaño de la memoria y otros datos en la opción **Sistema**:

![virt-manager](img/windows13.png)


## Instalación de Windows 11

TO DO
