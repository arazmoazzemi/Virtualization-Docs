

We can configure the network card in three modes.


### Network configs path:
```
cd /etc/libvirt/qemu/networks/
virsh net-list --all
```




sudo systemctl restart systemd-networkd &&
sudo ip link set vmbr1 down && sudo ip link set vmbr1 up &&
systemctl restart libvirtd

sudo /etc/init.d/networking restart

cat /var/log/syslog | grep kvm | grep network | grep vmbr1
cat /var/log/syslog | grep libvirtd | grep vmbr1





virsh net-destroy --network vmbr1
nano vmbr1.xml


<network>
  <name>vmbr1</name>  
  <forward mode='nat'/>
  <bridge name='vmbr1' stp='on' delay='0'/>  
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.122' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>


virsh net-define vmbr1.xml
virsh net-start vmbr1




