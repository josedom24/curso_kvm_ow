


cloud-img


wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img

cloud.yaml

#cloud-config
# Actualiza los paquetes
hostname: ubuntu-vm
package_update: true
package_upgrade: true
# Cambia las contraseña a los usuarios creados
chpasswd:
  expire: False
  users:
    - name: root
      password: newpassword
      type: text
    - name: ubuntu
      password: asdasd
      type: text



virt-install --connect qemu:///system --virt-type kvm --name ubuntu-vm   --memory 2048 --vcpus 2 --os-variant ubuntu24.04   --disk path=/var/lib/libvirt/images/noble-server-cloudimg-amd64.img,format=qcow2,bus=virtio     --graphics none --console pty,target_type=serial   --import --cloud-init user-data=/var/lib/libvirt/images/cloud.yaml   --noautoconsole


