# Gestión de pools de almacenamiento

Para ver los pools con la herramienta `virsh`, ejecutamos la siguiente instrucción:

```
usuario@kvm:~$ virsh pool-list 
 Nombre    Estado   Inicio automático
---------------------------------------
 default   activo   si
```

Recuerda que el pool por defecto donde se guardan las imágenes de disco, es `default`. Podemos obtener información de ese pool con la instrucción:

```
usuario@kvm:~$ virsh pool-info default 
Nombre:         default
UUID:           0a03e05b-8844-4029-8216-430fc289fe8f
Estado:         ejecutando
Persistente:    si
Autoinicio:     si
...
```

Al igual que las máquinas virtuales, los pools de almacenamiento se definen por un documento XML. Para ver la definición XML del pool `default` podemos ejecutar `virsh pool-dumpxml default`. A partir de un fichero XML con la definición de un nuevo pool, podríamos crearlo con el subcomando `virsh pool-define`. 

**Nota: Para profundizar en el formato XML que define los Pools de Almacenamiento puedes consultar la documentación oficial: [Storage pool and volume XML format](https://libvirt.org/formatstorage.html).**

Sin embargo, vamos a usar otro comando para crear pools de almacenamiento, que nos permite indicar la información del nuevo pool por medio de parámetros. Vamos a crear un nuevo pool que vamos a llamar `mv-images`, de tipo **dir** y cuyo directorio será `/srv/images`. Supongamos que hemos añadido más almacenamiento al host y que hemos montado el disco en el directorio `/srv/images` y queremos guardar las imágenes de disco en esa nueva localización. Para crear el nuevo pool, de forma persistente ejecutamos:

```
usuario@kvm:~$ virsh pool-define-as vm-images dir --target /srv/images
El grupo vm-images ha sido definido
```

**Nota: Si utilizamos `pool-create` o `pool-create-as`, el pool se crea temporalmente, no será persistente y después de un reinicio del host no existirá.**

A continuación creamos el directorio indicado, con la instrucción:

```
usuario@kvm:~$ virsh pool-build vm-images 
El pool vm-images ha sido compilado
```

Ahora debemos iniciar el pool:

```
usuario@kvm:~$ virsh pool-start vm-images 
Se ha iniciado el grupo vm-images
```

Y si lo deseamos lo podemos auto iniciar, para que en el reinicio del host vuelva a estar activo:

```
usuario@kvm:~$ virsh pool-autostart vm-images 
Se ha iniciado el grupo vm-images
```

Finalmente vemos la lista de pool y pedimos información del nuevo pool:

```
usuario@kvm:~$ virsh pool-list
 Nombre      Estado   Inicio automático
-----------------------------------------
 default     activo   si
 vm-images   activo   si

usuario@kvm:~$ virsh pool-info vm-images 
Nombre:         vm-images
UUID:           a9eb290a-9973-47ea-b616-0907a5df8ea2
Estado:         ejecutando
Persistente:    si
Autoinicio:     si
...
```

Ya podemos usar este pool de almacenamiento para guardar ficheros de imágenes de disco. Si en algún momento queremos eliminarlo, es recomendable pararlo:

```
usuario@kvm:~$ virsh pool-destroy vm-images 
El grupo vm-images ha sido destruid
```

A continuación, opcionalmente, podemos borrar el directorio creado:

```
usuario@kvm:~$ virsh pool-delete vm-images 
El grupo vm-images ha sido eliminado
```

Y por último lo eliminamos:

```
usuario@kvm:~$ virsh pool-undefine vm-images 
Se ha quitado la definición del grupo vm-images
```
