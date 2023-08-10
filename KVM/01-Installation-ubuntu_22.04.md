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


```
virt-host-validate
```
