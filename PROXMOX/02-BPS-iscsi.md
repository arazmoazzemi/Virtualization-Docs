### Connect the iSCSI target in your PBS server

For Example this is an An IQN is a standard way to uniquely identify iSCSI devices on a network:
```
iqn.2000-01.com.synology:save.Target-1.3bee535fbe
```

```
iscsiadm -m discovery -t st -p 192.168.31.99

iscsiadm -m node --targetname iqn.2000-01.com.synology:save.Target-1.3bee535fbe --portal 192.168.31.99 --login

iscsiadm -m session
```

```
iscsiadm -m node --targetname iqn.2000-01.com.synology:save.Target-1.3bee535fbe --portal 192.168.31.99 --op update --name node.startup --value automatic
```

```
mkdir /mnt/iscsi

sgdisk --zap-all /dev/sdb

mkfs.xfs -f /dev/sdb

blkid /dev/sdb

```

```
lsblk -d /dev/sdb
UUID=2fcbaaad-3e32-44be-8e7b-149c3f8fed76 /mnt/iscsi xfs _netdev 0 0

echo "UUID=2fcbaaad-3e32-44be-8e7b-149c3f8fed76 /mnt/iscsi xfs _netdev 0 0 ">> /etc/fstab
```

```
systemctl daemon-reload
```






