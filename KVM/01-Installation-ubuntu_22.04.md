# Install kvm on ubuntu 22.04
To install KVM on Ubuntu 22.04, you need to follow these steps:

*Make sure that your CPU supports hardware virtualization*

```
egrep -c '(vmx|svm)' /proc/cpuinfo
```

*Install packages and dependecies*
```
apt-get update && apt-get -y upgrade
sudo apt-get -y install libvirt-dev libvirt-daemon-system virtinst libvirt-clients virt-manager virt-viewer qemu qemu-kvm qemu-guest-agent bridge-utils oz libguestfs-tools uuid-runtime curl linux-source xauth ssh-askpass ssh-askpass-gnome libosinfo-bin  

```

*Enable libvirtd Service*
```
sudo systemctl enable libvirtd && systemctl start libvirtd
sudo systemctl status libvirtd
```

*Grant permissions*
```
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
```

*Check member of libvirt group*
```
getent group libvirt | awk -F: '{print $4}' |  tr "," " "
```

*Validation*
```
virt-host-validate
```

***Enable_IOMMU***
```
sudo nano /etc/default/grub

# Disable or comment below line
GRUB_CMDLINE_LINUX_DEFAULT="quiet"

# Add below lines :
# AMD_CPU
# GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on iommu=pt"

# Intel_CPU
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"
GRUB_CMDLINE_LINUX="rhgb systemd.unified_cgroup_hierarchy=0"
```

```
sudo update-grub
sudo reboot
```
*Check libvirt log*

```
journalctl -u libvirtd.service

```
*If faced with issue, after install virt-manager*

```
# 1 - Check Permission
getent group libvirt

# 2 - Give Permission
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER

# 3- Restart the server
newgrp libvirt
virt-manager
```


*install osinfo-db-tools*
```
sudo apt install osinfo-db-tools
wget -O "/tmp/osinfo-db.tar.xz" "https://releases.pagure.org/libosinfo/osinfo-db-20230518.tar.xz"
sudo osinfo-db-import --local "/tmp/osinfo-db.tar.xz"

osinfo-query os
```

*Enable port forwarding*
```
sysctl -p

sysctl -w net.ipv4.ip_forward=1
# OR
echo 1 > /proc/sys/net/ipv4/ip_forward

```

*stop iptables*
```
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -F
```

*disable cloud-init*
```
sudo touch /etc/cloud/cloud-init.disabled
```

*Enable nested virtualization*

Before enabling nested VT feature, power off all running VMs.
```
sudo modprobe -r kvm_intel
sudo modprobe kvm_intel nested=1

# Enable Nested Virtualization Permanently

sudo nano /etc/modprobe.d/kvm.conf
options kvm_intel nested=1

cat /sys/module/kvm_intel/parameters/nested
modinfo kvm_intel | grep -i nested


# Enable Nested Feature In KVM Guests Using Virt-manager

Copy host CPU configuration

Open Virt-manager GUI application and double click the KVM guest in which you want to enable nested VT feature.
 Click on the "Show virtual hardware details" button and go to the "CPUs" section in left menu.

Select the "Copy host CPU configuration" check box in the CPU configuration window and click Apply.

egrep --color -i "svm|vmx" /proc/cpuinfo
```

*make a custom bidge netwok*

*static ip address---enp1s0 interface*
```
nano /etc/netplan/00-installer-config.yaml

network:
  version: 2
  renderer: networkd

  ethernets:
    ens32:
      optional:
        true
      dhcp4: false
      dhcp6: false

  bridges:
    virbr0:
      optional:
        true
      interfaces: [ens32]
      addresses: [192.168.200.51/24]
      # gateway4 is deprecated, use routes instead
      routes:
      - to: default
        via: 192.168.200.2
        metric: 100
        on-link: true
      mtu: 1500
      nameservers:
        addresses: [8.8.8.8]
      parameters:
        stp: true
        forward-delay: 4
      dhcp4: no
      dhcp6: no

```

```
sudo netplan generate
sudo netplan --debug apply

sudo networkctl status virbr0
```

*make a custom bidge netwok*
```
brctl show

virsh net-list --all
virsh net-info default
virsh net-dumpxml default

# destroy and undefine every bridge networks
virsh net-destroy default
virsh net-undefine default
ip link delete virbr0

#add below

nano /etc/libvirt/qemu/networks/vmbr0.xml

<network>
  <name>vmbr0</name>
  <forward mode="bridge"/>
  <bridge name="vmbr0" />
</network>
```

```
cd /etc/libvirt/qemu/networks/
sudo mv /etc/libvirt/qemu/networks/default.xml default.xml.orig
sudo rm /etc/libvirt/qemu/networks/default.xml
virsh net-define --file /etc/libvirt/qemu/networks/vmbr0.xml

virsh net-start vmbr0
virsh net-autostart --network vmbr0

service libvirtd restart
```

*Preparing Machine Images for qemu/kvm*
*https://www.virtualizor.com/ostemplates/*
```
mkdir -p  /var/lib/libvirt/isos

wget https://releases.ubuntu.com/jammy/ubuntu-22.04.2-live-server-amd64.iso \
  -O /var/lib/libvirt/isos/ubuntu-22.04.2.iso
```

*vga installation*
```
virt-install \
  --name ubuntu-desktop \
  --connect=qemu:///system \
  --ram 8192 \
  --disk path=/var/lib/libvirt/images/ubuntu-desktop.qcow2,size=100 \
  --vcpu 4 \
  --graphics vnc \
  --cdrom /var/lib/libvirt/isos/ubuntu-22.04.2-desktop-amd64.iso 
  --network bridge=virbr0,model=virtio \
  --boot hd
```

*console installation*
```
virt-install \
  --name opennebula-cloud \
  --ram 8192 \
  --disk path=/var/lib/libvirt/images/opennebula-cloud.qcow2,size=30 \
  --vcpus 4 \
  --os-variant ubuntu22.04 \
  --network bridge=virbr0 \
  --graphics none \
  --console pty,target_type=serial \
  --location /var/lib/libvirt/isos/ubuntu-22.04.2-live-server-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \
  --extra-args 'console=ttyS0,115200n8' 
```

*raw disk format installation*
```
virt-install \
  --name ubuntu-server\
  --connect=qemu:///system \
  --ram 8192 \
  --disk path=/var/lib/libvirt/images/ubuntu-server.raw,format=raw,size=100 \
  --vcpu 4 \
  --graphics vnc \
  --cdrom /var/lib/libvirt/isos/ubuntu-22.04.2-live-server-amd64.iso 
  --network bridge=virbr0,model=virtio \
  --boot hd
```

*optional*
```
  --os-type=Linux \
  --disk path=image_file,bus=virtio,size=12 \
  
  --graphics none \
  --extra-args='console=ttyS0,115200n8 --- console=ttyS0,115200n8' \
  --debug
  --disk path=/var/lib/libvirt/images/ubuntu-22.04.2-desktop-amd64.raw,format=raw,bus=scsi,size=80 \

osinfo-query os

  --os-variant ubuntu22.04 \
```


*clone-kvm-host*
```
virt-clone --original ubuntu-template \
    --name ubuntu-22.04.2 \
    --file /var/lib/libvirt/images/ubuntu-22.04.2.qcow2
```






















