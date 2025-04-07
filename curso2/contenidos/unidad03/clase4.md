# Gestión de máquinas virtuales con virsh

En este apartado vamos a usar [virsh](https://libvirt.org/manpages/virsh.html) para gestionar las máquinas virtuales o dominios que hemos creado en los puntos anteriores. 

Para obtener ayuda sobre todos los comandos que podemos ejecutar:

```
usuario@kvm:~$ virsh --help
```

Si queremos pedir ayuda de un comando en concreto, por ejemplo el comando `list`, ejecutamos:

```
usuario@kvm:~$ virsh list --help
```

Ya hemos usado el comando `list` para mostrar las máquinas virtuales que tenemos creada:

```
usuario@kvm:~$ virsh list --all
 Id   Nombre    Estado
----------------------------
 2    debian12   ejecutando
```

**Nota: Podemos referencia una máquina virtual por su nombre o por su id.**

## Ciclo de vida de una máquina virtual

Para apagar de forma adecuada una máquina virtual:

```
usuario@kvm:~$ virsh shutdown debian12
```

Para iniciar una máquina que está detenida:

```
usuario@kvm:~$ virsh start debian12
```

Si la propiedad **autostart** de una maquina está activa, cada vez que se inicie el host, esa máquina se encenderá de forma automática. Para activarlo:

```
usuario@kvm:~$ virsh autostart debian12
```

Reiniciamos una máquina virtual, ejecutando:

```
usuario@kvm:~$ virsh reboot debian12
```

Podemos forzar el apagado de una máquina:

```
usuario@kvm:~$ virsh destroy debian12
```

Podemos pausar la ejecución de una máquina

```
usuario@kvm:~$ virsh suspend debian12
```

Y continuar la ejecución:

```
usuario@kvm:~$ virsh resume debian12
```

Por último, para eliminar una máquina virtual que esté parada (eliminando los volúmenes asociados):

```
usuario@kvm:~$ virsh undefine --remove-all-storage debian12
```

## Obtener información de la máquina virtual

Todos los comandos de `virsh` que empiezan por *dom* nos permiten obtener información de la máquina. 

Para obtener información de la máquina:

```
usuario@kvm:~$ virsh dominfo debian12 
```

Para obtener el estado de la máquina_

```
usuario@kvm:~$ virsh domstate debian12 
```

Para obtner la lista de interfaces de red  las direcciones IP de la máquina:

```
usuario@kvm:~$ virsh domiflist debian12
usuario@kvm:~$ virsh domifaddr debian12
```

Obtener los discos que tiene la máquina:

```
usuario@kvm:~$ virsh domblklist debian12
```

Para obtener estadísticas en tiempo real sobre CPU, memoria, disco y red.

```
usuario@kvm:~$ virsh domstats debian12
```


Puedes buscar [información](https://www.libvirt.org/manpages/virsh.html) de más comandos para obtener distinta información de la máquina virtual.