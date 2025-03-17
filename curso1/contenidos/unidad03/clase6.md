# Creación de máquinas virtuales Windows (2ª parte)

Una vez que hemos realizado la instalación del sistema operativo, podemos comprobar que la máquina no tiene acceso a internet, ya que tenemos que cargar los drivers de la tarjeta de red VirtIO que hemos configurado:

## Configuración de la red

Como indicábamos anteriormente, también hemos escogido el controlador VirtIO para la tarjeta de red. Una vez realizada la configuración tendremos que instalar los drivers adecuados para que funcione la tarjeta de red. Para ello, actualizamos el controlador del dispositivo **Controladora Ethernet** en el **Administrador de dispositivos**:

![virt-manager](img/virt-manager24.png)

Y escogemos la carpeta del CDROM donde hemos montado los drivers VirtIO: `NetKVM\<carpeta con el nombre de tu versión de windows>\amd64`:

![virt-manager](img/virt-manager25.png)

## Configuración de otros elementos hardware de la máquina



## Características de las máquina Windows que hemos creado



## Instalación de Windows 11

