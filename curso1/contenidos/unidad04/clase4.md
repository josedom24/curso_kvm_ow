# Trabajar con volúmenes en las máquinas virtuales

## Creación de máquinas virtuales usando volúmenes existentes

En el apartado anterior creamos un volumen de 10 GB llamado `vol1.qcow2`. Vamos a crear una nueva máquina virtual que tenga como disco duro este volumen. Para ello, durante el asistente de creación de la máquina, elegimos el volumen que tenemos creado:

![volumen](img/volumen3.png)

En este caso el volumen utilizado estará en el nuevo grupo de almacenamiento que hemos creado, por defecto los discos de las máquinas se crean en el grupo `default`.

## Añadir nuevos discos a máquinas virtuales

Hemos creado dos volúmenes de 1G en el grupo de almacenamiento `vm-images` llamados `vol2.qcow2` y `vol3.qcow` y lo vamos a añadir a las dos máquinas que tenemos creadas, una con Linux y otra con Windows. Esta operación se puede realizar con la máquina virtual ejecutándose. Si **añadimos nuevo hardware** en la vista detalle de la máquina, podemos añadir nuevo almacenamiento (repetimos esta operación en las dos máquinas):

![volumen](img/volumen4.png)

En el sistema Linux Debian, podemos utilizar la aplicación **discos** y vemos como se ha añadido el nuevo disco. Con esta herramienta podemos formatear el disco y posteriormente montarlo en nuestro sistema de archivos.

![volumen](img/volumen5.png)

En el sistema Windows, podemos utilizar la aplicación **Administración de discos** y realizar las mismas operaciones.

![volumen](img/volumen6.png)

![volumen](img/volumen7.png)


Para desconectar un disco de una máquina virtual, simplemente pulsaríamos con el botón derecho sobre el dispositivo de disco en la vista detalle, y pulsaríamos sobre **Eliminar Hardware**.

