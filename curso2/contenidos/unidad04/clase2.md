# Gestión de pools de almacenamiento

Para ver los pools con la herramienta `virsh`, ejecutamos la siguiente instrucción:

```
usuario@kvm:~$ virsh pool-list 
 Nombre    Estado   Inicio automático
---------------------------------------
 default   activo   si
```

Recuerda que por defecto las imágenes de disco se guardan en el directorio `/var/lib/libvirt/images` y que creamos un pool de almacenamiento de tipo **dir**, correspondiente a este directorio, que llamamos `default`. Para ver la información de este pool podemos ejecutar:

```
usuario@kvm:~$ virsh pool-info default 
```

Al igual que las máquinas virtuales, los pools de almacenamiento se definen por un documento XML. Para ver la definición XML del pool `default` podemos ejecutar `virsh pool-dumpxml default`. A partir de un fichero XML con la definición de un nuevo pool, podríamos crearlo con el subcomando `virsh pool-define`. 

**Nota: Para profundizar en el formato XML que define los Pools de almacenamiento puedes consultar la documentación oficial: [Storage pool and volume XML format](https://libvirt.org/formatstorage.html).**

Sin embargo, vamos a usar otro comando para crear pools de almacenamiento, que nos permite indicar la información del nuevo pool por medio de parámetros. Vamos a crear un nuevo pool que vamos a llamar `mv-images`, de tipo **dir** y cuyo directorio será `/srv/images`. Supongamos que hemos añadido más almacenamiento al host y que hemos montado el disco en el directorio `/srv/images` y queremos guardar las imágenes de disco en esa nueva localización. Para crear el nuevo pool, de forma persistente ejecutamos:

```
usuario@kvm:~$ virsh pool-define-as vm-images dir --target /srv/images
```

**Nota: Si utilizamos `pool-create` o `pool-create-as`, el pool se crea temporalmente, no será persistente y después de un reinicio del host no existirá.**

A continuación creamos el directorio indicado, con la instrucción:

```
usuario@kvm:~$ virsh pool-build vm-images 
```

Ahora debemos iniciar el pool:

```
usuario@kvm:~$ virsh pool-start vm-images 
```

Y si lo deseamos lo podemos autoiniciar, para que en el reinicio del host vuelva a estar activo:

```
usuario@kvm:~$ virsh pool-autostart vm-images 
```

Finalmente, vemos la lista de pool y pedimos información del nuevo pool:

```
usuario@kvm:~$ virsh pool-list
usuario@kvm:~$ virsh pool-info vm-images 
```

Ya podemos usar este pool de almacenamiento para guardar ficheros de imágenes de disco. Si en algún momento queremos eliminarlo, es recomendable pararlo:

```
usuario@kvm:~$ virsh pool-destroy vm-images 
```

A continuación, opcionalmente, podemos borrar el directorio creado:

```
usuario@kvm:~$ virsh pool-delete vm-images 
```

Y por último lo eliminamos:

```
usuario@kvm:~$ virsh pool-undefine vm-images 
```
