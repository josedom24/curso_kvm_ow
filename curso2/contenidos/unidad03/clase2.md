# Características de las máquinas virtuales

Después de instalar nuestra primera máquina, podemos comprobar la lista de máquinas ejecutando la siguiente instrucción:

```
usuario@kvm:~$ virsh list
 Id   Nombre    Estado
----------------------------
 2    debian12   ejecutando
```

Sie estamos trabajando en un sistema con entorno gráfico podemos usar la herramienta `virt-viewer` para conectarno  a la consola de la máquina:

```
usuario@kvm:~$ virt-viewer debian12
```

## Red

Como comentábamos en el punto anterior, la máquina que hemos creado se conecta, por defecto, a la red `default`. Esta red es de tipo NAT, y comprobamos que la máquina ha recibido una IP de forma dinámica y que su puerta de enlace corresponde a la dirección IP `192.168.122.1`, que corresponde con el host, el servidor DNS corresponde a la misma IP y comprobamos que tiene resolución y acceso a internet:

![características](img/caracteristica1.png)

## Recursos hardware

Podemos comprobar que la máquina tiene un disco de 10 Gb y de memoria RAM 1Gb:

![características](img/caracteristica2.png)

## Almacenamiento

Un **Pool de almacenamiento** es un recurso de almacenamiento. Lo más usual es tener pools de almacenamiento que sean locales, por ejemplo un directorio. Pode efecto tenemos el un pool llamado `default`, que corresponde con el directorio `/usr/lib/libvirt/images` y donde se guardarán los ficheros correspondientes a las imágenes de disco.

Podemos ver los pools de almacenamiento, que tenemos creado, ejecutando:

```
usuario@kvm:~$ virsh pool-list 
 Nombre    Estado   Inicio automático
---------------------------------------
 default   activo   si
```

Un **volumen** es un medio de almacenamiento que podemos crear en un pool de almacenamiento en kvm. Si el pool de almacenamiento es de tipo *dir*, entonces el volumen será un fichero de imagen.

Veamos el volumen que se ha creado el pool `default`:

```
usuario@kvm:~$ virsh vol-list default
 Nombre          Ruta
--------------------------------------------------------
 debian12.qcow2   /var/lib/libvirt/images/debian12.qcow2
 debian-12.10.0-amd64-netinst.iso   /home/usuario/iso/debian-12.10.0-amd64-netinst.iso
```
En todos estos conceptos sobre almacenamiento profundizaremos en el módulo correspondiente.