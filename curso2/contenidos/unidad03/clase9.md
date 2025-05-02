# Acceso a las máquinas virtuales desde el exterior

Una vez que hemos creado las máquinas con `virt-install` podemos acceder a ellas utilizando la aplicación `virt-viewer`. Sin embargo, es posible acceder a ellas utilizando otros protocolos específicos de acceso remoto, que suelen ser más eficientes para el trabajo con las máquinas.

## Acceso por SSH a las máquinas Linux

El protocolo más habitual para trabajar con máquinas Linux es SSH. Podemos instalar el servidor SSH durante la instalación del sistema operativo. Si no lo hemos hecho, podríamos instalarlo en distribuciones Debian/Ubuntu ejecutando:

```
usuario@debian:~$ apt install ssh
```

Una vez que sabemos la dirección IP de la máquina:

```
usuario@kvm:~$ virsh domifaddr debian12
virsh domifaddr debian12
 Name       MAC address          Protocol     Address
-------------------------------------------------------------------------------
 vnet0      52:54:00:49:1c:8f    ipv4         192.168.122.59/24

```

Desde nuestro equipo podemos acceder a esta máquina por SSH indicando el nombre de usuario y la IP:

```
usuario@kvm:~$ ssh usuario@192.168.122.59
```

## Acceso por RDP a una máquina Windows

Normalmente, para acceder a las máquinas Windows usamos el protocolo RDP (Remote Desktop Protocol). Para acceder necesitamos usar un cliente RDP. Por ejemplo, en sistemas Linux puedes usar [Remmina](https://remmina.org/), si lo haces desde un sistema Windows puedes usar "Conexión a Escritorio remoto".

En este ejemplo voy a usar el cliente Remmina.

Hay que indicar que las versiones más sencillas como Windows 10 Home no tienen la posibilidad del acceso remoto, por lo tanto, vamos a utilizar una versión Windows 10 Pro. Lo primero que tenemos que hacer es configurar Windows para permitir el acceso remoto. Para ello elegimos **Inicio > Configuración  > Sistema > Escritorio remoto** y activa **Habilitar escritorio remoto**.

![acceso](img/acceso2.png)

A continuación, configuramos el cliente remmina con una nueva conexión, indicando la dirección IP de la máquina, el usuario y la contraseña y la resolución de pantalla:

![acceso](img/acceso3.png)

Y ya podemos conectar para acceder a la máquina:

![acceso](img/acceso4.png)

