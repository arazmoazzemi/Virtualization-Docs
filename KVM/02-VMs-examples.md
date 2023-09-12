Example for zabbix-6.4.6 installation:

```bash
virt-install \
  --import --name zabbix \
  --memory 8192 \
  --vcpus 4 \
  --cpu host \
  --disk /var/lib/libvirt/images/zabbix-6.4.6.qcow2,format=qcow2,bus=virtio \
  --network bridge=virbr0,model=virtio \
  --osinfo detect=on,require=off \
  --graphics none \
  --noautoconsole \
```
