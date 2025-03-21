# Conexión local no privilegiada a libvirt

furante todo el curso hemos hecho uso de la conexión privilegiada a libvirt y por tanto es el superusuario el que ha gestionado los recursos virtualizados.

En este apartado vamos a comprobar que un usuario sin privilegio puede crear sus máquinas virtuales. Para ello realizará una conexión local a libvirt usando la URI `qemu:///session`. En este modo de conexión, el usuario no tiene permisos para crear conexiones de red, por lo que se limita su uso de la red no privilegiada de QEMU ([SLIRP](https://wiki.qemu.org/Documentation/Networking#User_Networking_.28SLIRP.29)) que es útil para casos simples, pero que tiene bajo rendimiento y es poco configurable. 

## Configuración de virt-manager para una conexión no privilegiada

Desde virt-manager podemos crear una nueva conexión no privilegiada, eligiendo la opción **Archivo - Añadir conexión...** y eligiendo como **Hipervisor** la opción **QEMU/LMV sesión de usuario**.

IMAGEN NUEVA CONEXIÓN

Si seleccionamos la conexión que acabamos de añadir y escogemos la opción **Detalles** podemos fijarnos en los siguientes aspectos:

* No tenemos configurada ninguna red virtual y si intentamos crear una nueva nos va a dar un error, ya que el usuario sin privilegio no puede crear nuevas redes.

    IMAGEN REDES

* Tenemos un grupo de almacenamiento llamado `default` de tipo directorio, donde observamos que los ficheros de imágenes de discos de las máquinas virtuales se guardarán en un directorio edl usuario. En mi caso que uso el usuario llamado `usuario` este directorio sería: `/home/usuario/.local/share/libvirt/images`.

    IMAGEN ALMACENAMIENTO

## Creación de una máquina virtual

El procedimiento para crear una nueva máquina virtual es similar al descrito en apartados anteriores a este apartado, teniendo en cuenta los siguientes aspectos:

* Aunque no podemos conectar la máquina a una red virtual (sólo nos permite seleccionar **Modo usuario de creación de redes**) si podemos conectarla a un puente externo a conectarla al exterior compartiendo una interfaz física (macvtap).

    IMAGEN SELECCIONAR REDES

