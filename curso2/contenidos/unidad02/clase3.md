

## Conexión a QEMU/KVM

Vamos a usar la utilidad `virsh`, que nos proporciona una shell completa para el manejo de libvirt. Con el comando `list` mostramos las máquinas virtuales que hemos creado.

Con un usuario sin privilegios ejecutamos:

```
usuario@kvm:~$ virsh list
```

Estaríamos haciendo una conexión local con un usuario no privilegiado (estaríamos conectando con la URI `qemu:///session` y estaríamos mostrando las máquinas virtuales de este usuario.

Si por el contrario, como `root` ejecutamos:

```
root@kvm:~# virsh list
```

Estaríamos haciendo una conexión local privilegiada (estaríamos conectando con la URI `qemu:///system`) y mostraríamos las máquinas virtuales del sistema.

Si queremos que un usuario sin privilegios pueda hacer conexiones privilegiadas, el usuario debe pertenecer el grupo `libvirt`:

```
root@kvm:~# adduser usuario libvirt
```

Para que el usuario `usuario` haga una conexión privilegiada tendrá que indicar explícitamente la conexión a la URI `qemu:///system`:

```
usuario@kvm:~$ virsh -c qemu:///system list
```

De forma alternativa y según la [wiki de Debian](https://wiki.debian.org/es/KVM#M.2BAOE-quinas_virtuales_del_usuario_y_del_sistema), podemos usar la variable de entorno `LIBVIRT_DEFAULT_URI` con el siguiente comando:

```bash
export LIBVIRT_DEFAULT_URI='qemu:///system'
```
