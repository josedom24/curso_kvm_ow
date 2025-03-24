## **📌 Métodos para crear plantillas en KVM/Libvirt**
Tienes dos enfoques principales:  

1️⃣ **Usar una imagen QCOW2 como base con backing storage** (como los linked clones en Proxmox).  
2️⃣ **Convertir una VM en plantilla y clonarla** (como los full clones en Proxmox).  

---

## **1️⃣ Método con Backing Storage (Linked Clone)**
Este método usa un disco base (`qcow2`) como plantilla y crea discos nuevos vinculados a él.  
✅ **Ventaja**: Ahorra espacio, ya que los clones solo almacenan los cambios.  
⚠️ **Desventaja**: Si el disco base se daña o elimina, todas las VMs fallarán.  

### **🔹 1. Crear una imagen base (plantilla)**
Puedes usar una imagen cloud-init como plantilla, por ejemplo, Ubuntu Cloud Image:  
```sh
wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img \
     -O /var/lib/libvirt/images/ubuntu-template.qcow2
```
💡 **Opcional**: Redimensionar el disco si lo necesitas:
```sh
qemu-img resize /var/lib/libvirt/images/ubuntu-template.qcow2 +10G
```

### **🔹 2. Crear una nueva imagen con backing storage**
```sh
qemu-img create -f qcow2 -b /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-vm1.qcow2
```
📌 Esto crea un nuevo disco (`ubuntu-vm1.qcow2`), pero los datos base siguen en `ubuntu-template.qcow2`.  

### **🔹 3. Crear la VM con el nuevo disco**
```sh
virt-install --connect qemu:///system --name ubuntu-vm1 \
    --memory 2048 --vcpus 2 --disk path=/var/lib/libvirt/images/ubuntu-vm1.qcow2,format=qcow2 \
    --os-variant ubuntu24.04 --import --network network=default --noautoconsole
```
🎯 **Este método es eficiente en espacio, pero dependiente del disco base.**  

---

## **2️⃣ Método con Clonación Completa (Full Clone)**
Este método copia completamente la imagen base, por lo que las VMs son independientes.  
✅ **Ventaja**: No dependen del disco base.  
⚠️ **Desventaja**: Consumen más almacenamiento.  

### **🔹 1. Copiar la plantilla a un nuevo disco**
```sh
qemu-img create -f qcow2 -F qcow2 -b /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-vm1.qcow2
```
**O hacer una copia completa con:**
```sh
qemu-img convert -f qcow2 -O qcow2 /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-vm1.qcow2
```
📌 **La opción `convert` genera una copia independiente**.  

### **🔹 2. Crear la VM con el nuevo disco**
```sh
virt-install --connect qemu:///system --name ubuntu-vm1 \
    --memory 2048 --vcpus 2 --disk path=/var/lib/libvirt/images/ubuntu-vm1.qcow2,format=qcow2 \
    --os-variant ubuntu24.04 --import --network network=default --noautoconsole
```

---

## **📌 Comparación entre métodos**
| Método                  | Espacio en disco | Independencia | Rendimiento |
|-------------------------|-----------------|--------------|-------------|
| **Backing Storage**      | Ahorra espacio  | Depende de la plantilla | Rápido, pero puede afectar rendimiento si muchos VMs acceden al disco base |
| **Clonación Completa**   | Más consumo de espacio | Totalmente independiente | Sin impacto en rendimiento |

---

## **📌 Opción con Cloud-Init**
Si usas **Cloud-Init**, puedes generar VMs preconfiguradas sin necesidad de intervención manual.  
Ejemplo:
```sh
virt-install --connect qemu:///system --name ubuntu-vm1 \
    --memory 2048 --vcpus 2 --disk path=/var/lib/libvirt/images/ubuntu-vm1.qcow2,format=qcow2 \
    --os-variant ubuntu24.04 --cloud-init user-data=cloud-init.yaml \
    --import --network network=default --noautoconsole
```
📌 Donde `cloud-init.yaml` define usuarios, claves SSH y configuración inicial.

---

## **📌 Conclusión**
✅ **Para ahorrar espacio**, usa **backing storage** (`-b` en `qemu-img create`).  
✅ **Para independencia total**, usa **clonación completa** (`qemu-img convert`).  
✅ **Si necesitas configuración automática**, usa **Cloud-Init**.  

¿Quieres que te ayude con un `cloud-init.yaml` para personalizar las VMs? 🚀


--------------------------------


Para crear una **imagen base** en KVM/Libvirt que sirva como plantilla reutilizable, puedes seguir estos pasos. El objetivo es tener una imagen **configurada y lista para clonar** usando backing storage o clonación completa.

---

# **📌 Pasos para Crear una Imagen Base en KVM/Libvirt**

### **1️⃣ Descarga una imagen base (opcional)**
Si quieres partir de una imagen oficial de Ubuntu/Debian con Cloud-Init:  
```sh
wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img \
     -O /var/lib/libvirt/images/ubuntu-template.qcow2
```
☑️ Esta imagen es compatible con Cloud-Init y ya está optimizada.

---

### **2️⃣ Crear una VM manualmente (alternativa sin Cloud-Init)**
Si prefieres instalar desde cero, usa `virt-install` con una ISO:

```sh
virt-install --connect qemu:///system --name ubuntu-template \
    --memory 2048 --vcpus 2 \
    --disk path=/var/lib/libvirt/images/ubuntu-template.qcow2,size=10,format=qcow2 \
    --cdrom /var/lib/libvirt/images/ubuntu-24.04.2-live-server-amd64.iso \
    --network network=default --os-variant ubuntu24.04 \
    --graphics none --console pty,target_type=serial
```
📌 Instala Ubuntu/Debian normalmente, **pero no configures usuarios**.

---

### **3️⃣ Configurar la plantilla (Optimización y Cloud-Init)**  
Después de instalar, accede a la VM:
```sh
virsh console ubuntu-template
```
Dentro de la VM, ejecuta:
```sh
sudo apt update && sudo apt upgrade -y
sudo apt install cloud-init -y
sudo cloud-init clean
```
📌 **Esto borra configuraciones previas para que Cloud-Init funcione en los clones.**  

---

### **4️⃣ Convertir la VM en plantilla**
Detén la máquina:
```sh
virsh shutdown ubuntu-template
```
Para evitar modificaciones accidentales, hazla **de solo lectura**:
```sh
chmod 444 /var/lib/libvirt/images/ubuntu-template.qcow2
```
📌 **Esta imagen base ahora está lista para clonar**.

---

# **📌 Crear VMs desde la Imagen Base**

## **🔹 Opción 1: Backing Storage (Linked Clone)**
```sh
qemu-img create -f qcow2 -b /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-vm1.qcow2
```
Y lanzar la VM:
```sh
virt-install --connect qemu:///system --name ubuntu-vm1 \
    --memory 2048 --vcpus 2 --disk path=/var/lib/libvirt/images/ubuntu-vm1.qcow2,format=qcow2 \
    --os-variant ubuntu24.04 --import --network network=default --noautoconsole
```

## **🔹 Opción 2: Clonación Completa**
Si quieres VMs independientes, usa:
```sh
qemu-img convert -f qcow2 -O qcow2 /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-vm1.qcow2
```
Y luego crea la VM como antes.

---

# **📌 Conclusión**
✅ **Cloud-Init permite automatizar la configuración en clones.**  
✅ **Backing storage ahorra espacio, pero las VMs dependen de la plantilla.**  
✅ **La clonación completa genera VMs independientes.**  

🚀 **¿Necesitas ayuda con `cloud-init.yaml` para que las VMs se configuren solas?**


-----

Sí, esa es una excelente estrategia para crear una imagen base optimizada y reutilizable. Te explico cómo usar **`virt-sysprep`** y **`virt-sparsify`** para preparar una plantilla en KVM/Libvirt.

---

# **📌 Creación de una Imagen Base con `virt-sysprep` y `virt-sparsify`**

## **1️⃣ Crear la Imagen Base**
Puedes partir de una instalación manual o una imagen oficial.

**Opción 1: Instalar desde una ISO y luego limpiar con `virt-sysprep`**  
Si instalaste Ubuntu/Debian manualmente en una VM, **apaga la máquina**:
```sh
virsh shutdown ubuntu-template
```
Y convierte el disco en plantilla.

**Opción 2: Partir de una Imagen Cloud**
Si prefieres usar una imagen oficial, descárgala y usa:
```sh
cp noble-server-cloudimg-amd64.img /var/lib/libvirt/images/ubuntu-template.qcow2
```

---

## **2️⃣ Limpiar la Imagen Base con `virt-sysprep`**
Ejecuta:
```sh
virt-sysprep -a /var/lib/libvirt/images/ubuntu-template.qcow2
```
Esto elimina:

✅ **Usuarios y contraseñas**  
✅ **Registros SSH**  
✅ **Logs y datos temporales**  
✅ **UUIDs de red** (evita problemas con `eth0` en clones)  
✅ **Configuraciones específicas de la máquina**  

Opcionalmente, puedes eliminar ciertos datos manualmente:
```sh
virt-sysprep -a /var/lib/libvirt/images/ubuntu-template.qcow2 --operations defaults,-ssh-host-keys,-udev-persistent-net
```
⚡ **Esto mantiene las claves SSH del host y evita que cambie `eth0`.**

---

## **3️⃣ Reducir el Tamaño con `virt-sparsify`**  
Para compactar la imagen y ahorrar espacio:
```sh
virt-sparsify --compress /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-template-sparse.qcow2
```
Luego reemplázala:
```sh
mv /var/lib/libvirt/images/ubuntu-template-sparse.qcow2 \
   /var/lib/libvirt/images/ubuntu-template.qcow2
```
🚀 **Esto elimina bloques vacíos y reduce el tamaño en disco.**

---

## **4️⃣ Convertir la Imagen en Plantilla**
Hazla de **solo lectura** para evitar cambios accidentales:
```sh
chmod 444 /var/lib/libvirt/images/ubuntu-template.qcow2
```

---

## **📌 Creando VMs desde la Plantilla**

### **🔹 Opción 1: Backing Storage (Linked Clone)**
```sh
qemu-img create -f qcow2 -b /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-vm1.qcow2
```
Y luego:
```sh
virt-install --connect qemu:///system --name ubuntu-vm1 \
    --memory 2048 --vcpus 2 \
    --disk path=/var/lib/libvirt/images/ubuntu-vm1.qcow2,format=qcow2 \
    --os-variant ubuntu24.04 --import --network network=default --noautoconsole
```

### **🔹 Opción 2: Clonación Completa**
Si quieres una VM totalmente independiente:
```sh
qemu-img convert -f qcow2 -O qcow2 /var/lib/libvirt/images/ubuntu-template.qcow2 \
    /var/lib/libvirt/images/ubuntu-vm1.qcow2
```
Y luego crear la VM como antes.

---

# **📌 Conclusión**
✅ **`virt-sysprep` limpia la imagen para evitar conflictos en clones.**  
✅ **`virt-sparsify` reduce el tamaño eliminando datos vacíos.**  
✅ **Puedes usar backing storage o clones completos según necesites.**  

🚀 ¿Quieres agregar un `cloud-init` personalizado en los clones?