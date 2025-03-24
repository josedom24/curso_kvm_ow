

virt-install --connect qemu:///system \
    --virt-type kvm \
    --name debian12 \
    --location http://deb.debian.org/debian/dists/bookworm/main/installer-amd64/ \
    --os-variant debian12 \
    --disk size=10 \
    --memory 1024 \
    --vcpus 1 \
    --graphics none --console pty,target_type=serial \
    --extra-args="console=ttyS0,115200n8"



Debian	http://deb.debian.org/debian/dists/bookworm/main/installer-amd64/
CentOS	http://mirror.centos.org/centos/9-stream/BaseOS/x86_64/os/
Rocky Linux	http://download.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os/
AlmaLinux	http://repo.almalinux.org/almalinux/9/BaseOS/x86_64/os/
Fedora	https://download.fedoraproject.org/pub/fedora/linux/releases/39/Everything/x86_64/os/
Arch Linux	http://mirror.rackspace.com/archlinux/iso/latest/


virt-install --connect qemu:///system \
    --virt-type kvm \
    --name fed \
    --location https://download.fedoraproject.org/pub/fedora/linux/releases/40/Everything/x86_64/os/ \
    --os-variant fedora39 \
    --disk size=10 \
    --memory 1024 \
    --vcpus 1 \
    --graphics none --console pty,target_type=serial \
    --extra-args="console=ttyS0,115200n8"





virt-builder debian-12 \
  --size 10G \
  --format qcow2 \
  --output /var/lib/libvirt/images/debian-12.qcow2 \
  --hostname mimaquina \
  --install "openssh-server,vim" \
  --root-password password:asdasd \
  --run-command "sed -i 's/^XKBLAYOUT=.*/XKBLAYOUT=\"es\"/' /etc/default/keyboard" \
  --run-command "dpkg-reconfigure -f noninteractive keyboard-configuration" \
  --firstboot-command "dpkg-reconfigure openssh-server" \
  --run-command "sed -i 's/enp2/enp1s0/g' /etc/network/interfaces"


https://devtechs.readthedocs.io/en/latest/topics/virtualization/qemu-kvm/virt-builder.html


sudo apt install libguestfs-tools
