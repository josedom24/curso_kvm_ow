# Instantáneas de máquinas virtuales

Una instantánea (snapshot) nos posibilita guardar el estado de una máquina virtual en un determinado momento. Se guarda el estado del disco y el estado de la memoria. De esta forma en el futuro puedo volver a un estado anterior de la misma. No todos los formatos y medios de almacenamiento nos posibilitan esta características. Un fichero de imagen de disco con formato `qcow2` si nos permite la realización de instantáneas.

Hemos hecho un cambio significativo en nuestra máquina (en el ejemplo hemos creado una carpeta). 

![instantanea](img/instantanea1.png)

Ahora es el momento de crear una instantánea, de esta manera podremos volver a este estado en un momento futuro. Accediendo a la **Administrar instantáneas de máquina virtual** obtenemos la ventana para gestionar las instantáneas:

![instantanea](img/instantanea2.png)

Tenemos botones para las opción más comunes:

* **Botón 1**: Crear instantánea.
* **Botón 2**: Volver al estado de la instantánea seleccionada.
* **Botón 3**: Refrescar la lista de instantáneas.
* **Botón 4**: Borrar la instantánea seleccionada.

Podemos crear una instantánea con la máquina en ejecución o con la máquina parada. Si la máquina está en ejecución se pausará mientras se realiza la instantánea. Al crear una instantánea, podemos indicar el nombre, la descripción y se guarda una captura de pantalla de la máquina.

![instantanea](img/instantanea3.png)


Si hemos tenido un problema en nuestra máquina y hemos eliminado nuestra carpeta importante:

![instantanea](img/instantanea4.png)

Podemos recuperar la instantánea y volver al estado anterior.

Puede ser muy interesante tomar instantáneas periódicamente a una máquina virtual. Si tenemos cualquier problema con la máquina podemos volver a un estado estable anterior.
Esta característica puede ser muy útil, ya que nos permite experimentar con la máquina, y si tenemos algún problema, podemos volver al estado original y no tener que eliminar la máquina.

