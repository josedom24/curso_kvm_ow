

virt-builder debian-12 \
  --size 10G \
  --format qcow2 \
  --output /var/lib/libvirt/images/mi_debian12.qcow2 \
  --hostname mimaquina \
  --root-password password:asdasd \
  --run-command "sed -i 's/^XKBLAYOUT=.*/XKBLAYOUT=\"es\"/' /etc/default/keyboard" \
  --run-command "dpkg-reconfigure -f noninteractive keyboard-configuration" \
  --run-command "sed -i 's/ens2/enp1s0/g' /etc/network/interfaces" \
  --firstboot-command 'useradd -m -p "" -s /bin/bash usuario ; chage -d 0 usuario' \
  --firstboot-command "dpkg-reconfigure openssh-server" \
  --firstboot-command "systemctl enable --now getty@ttyS0" 
  


virt-install --connect qemu:///system \
    --virt-type kvm \
    --name debian12-builder \
    --disk path=/var/lib/libvirt/images/midebian12.qcow2,format=qcow2 \
    --os-variant debian12 
    --memory 2048 \
    --vcpus 2 
    --import --graphics none

También se puede probar --noautoconsole

virsh domifaddr midebian12 

 ssh usuario@x.x.x.x



https://devtechs.readthedocs.io/en/latest/topics/virtualization/qemu-kvm/virt-builder.html


sudo apt install libguestfs-tools

-----------------

cloud-img


wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img

cloud.yaml

#cloud-config
# Actualiza los paquetes
hostname: ubuntu-vm
package_update: true
package_upgrade: true
# Cambia las contrase�a a los usuarios creados
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


