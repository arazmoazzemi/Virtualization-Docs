# Install kvm on ubuntu 22.04

*Install packages*
```
apt-get update && apt-get -y upgrade
sudo apt-get -y install libvirt-dev libvirt-daemon-system virtinst libvirt-clients virt-manager virt-viewer qemu qemu-kvm qemu-guest-agent bridge-utils oz libguestfs-tools uuid-runtime curl linux-source xauth ssh-askpass ssh-askpass-gnome libosinfo-bin  

```

*Enable libvirtd Srvice*
```
sudo systemctl enable libvirtd && systemctl start libvirtd
sudo systemctl status libvirtd
```

*Grant permissions*
`
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
`

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

#GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on iommu=pt"
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"

GRUB_CMDLINE_LINUX="rhgb systemd.unified_cgroup_hierarchy=0"
```

```
update-grub
```

*install osinfo-db-tools*
```
sudo apt install osinfo-db-tools
wget -O "/tmp/osinfo-db.tar.xz" "https://releases.pagure.org/libosinfo/osinfo-db-20230518.tar.xz"
sudo osinfo-db-import --local "/tmp/osinfo-db.tar.xz"

osinfo-query os
```

*enable port forwarding*
```
sysctl -p
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




























