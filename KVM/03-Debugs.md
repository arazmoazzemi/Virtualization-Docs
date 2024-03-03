### KVM Debugs:

 We can configure the network card in three modes:
 
 1- Bridge mode
 
 2- Nat mode
 
 3- host mode

----
### Network configs path:

```bash
cd /etc/libvirt/qemu/networks/
virsh net-list --all
```
----


### Network Logs:

```bash
cat /var/log/syslog | grep kvm | grep network | grep <Bridge_Name>
cat /var/log/syslog | grep libvirtd | grep <Bridge_Name>
```



### How Can I add Nat mode adapter:
### For example:

```
brctl addbr vmbr4
```
```
touch /etc/libvirt/qemu/networks/vmbr4.xml
nano /etc/libvirt/qemu/networks/vmbr4.xml

<network>
  <name>vmbr4</name>  
  <forward mode='nat'/>
  <bridge name='vmbr4' stp='on' delay='0'/>  
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.122' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>
```

```
virsh net-define --validate --file vmbr4.xml
```

### ❗ Important Note: 👉Restart KVM HOST and 

```
virsh net-start vmbr1
```



