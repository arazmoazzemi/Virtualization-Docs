

Connect the iSCSI target in your PBS server

iqn.2000-01.com.synology:save.Target-1.3bee535fbe
iqn.2000-01.com.synology:save.Target-1.3bee535fbe

iscsiadm -m discovery -t st -p 192.168.31.99

iscsiadm -m node --targetname iqn.2000-01.com.synology:save.Target-1.3bee535fbe --portal 192.168.31.99 --login

iscsiadm -m session

iscsiadm -m node --targetname iqn.2000-01.com.synology:save.Target-1.3bee535fbe --portal 192.168.31.99 --op update --name node.startup --value automatic

mkdir /mnt/iscsi

sgdisk --zap-all /dev/sdb

mkfs.xfs -f /dev/sdb

blkid /dev/sdb

```

/dev/sdb: UUID="2fcbaaad-3e32-44be-8e7b-149c3f8fed76" BLOCK_SIZE="512" TYPE="xfs"

UUID=2fcbaaad-3e32-44be-8e7b-149c3f8fed76 /mnt/iscsi xfs _netdev 0 0



```

mount /dev/sdb /mnt/iscsi

lsblk -d /dev/sdb

```
iscsiadm --mode node -T iqn.2000-01.com.synology:save.Target-1.3bee535fbe -p 192.168.31.99 -o update -n node.startup -v automatic
iscsiadm --mode node -T iqn.2000-01.com.synology:save.Target-1.3bee535fbe -p 192.168.31.99 -o update -n node.conn[0].startup -v automatic
sed -i '/^node.startup = /s/manual/automatic/g' /etc/iscsi/iscsid.conf
```

```
systemctl daemon-reload
```
-----------


iscsiadm -m discovery -t sendtargets -p 192.168.31.99
iscsiadm -m node --targetname "<target_name>" --portal "<target_ip_address>:<port>" --login

iscsiadm -m session




echo "- - -" > /sys/class/scsi_host/hostX/scan






