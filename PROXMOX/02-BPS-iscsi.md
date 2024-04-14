### Connect the iSCSI target in your PBS server

For Example this is an An IQN is a standard way to uniquely identify iSCSI devices on a network:
```
iqn.2000-01.com.synology:save.Target-1.3bee535fbe
```

1. Discover iSCSI Targets
Use the iscsiadm command to discover iSCSI targets available on the network. This step allows the PBS server to identify the target it wants to connect to.
```
iscsiadm -m discovery -t st -p 192.168.31.99
```

2. Log in to the Target
After discovering the target, log in to it using its unique iSCSI Qualified Name (IQN) and the IP address of the portal.
```
iscsiadm -m node --targetname iqn.2000-01.com.synology:save.Target-1.3bee535fbe --portal 192.168.31.99 --login
```

3. Verify Session
Check the iSCSI session to ensure successful connection and authentication.
```
iscsiadm -m session
```

4. Set Node Startup
Configure the node to automatically start up the iSCSI connection upon system boot.
```
iscsiadm -m node --targetname iqn.2000-01.com.synology:save.Target-1.3bee535fbe --portal 192.168.31.99 --op update --name node.startup --value automatic
```

5. Prepare Disk
Prepare the disk for use by creating a mount point, clearing any existing partition table, and formatting it with the desired filesystem.
```
mkdir /mnt/iscsi

sgdisk --zap-all /dev/sdb

mkfs.xfs -f /dev/sdb
```

6. Identify Disk UUID
Identify the UUID (Universally Unique Identifier) of the formatted disk.
```
blkid /dev/sdb
```

7. Mount Disk
Mount the iSCSI disk to the previously created mount point and configure it to mount automatically on system boot by adding an entry to /etc/fstab.
```
lsblk -d /dev/sdb
UUID=2fcbaaad-3e32-44be-8e7b-149c3f8fed76 /mnt/iscsi xfs _netdev 0 0

echo "UUID=2fcbaaad-3e32-44be-8e7b-149c3f8fed76 /mnt/iscsi xfs _netdev 0 0 ">> /etc/fstab
```

8. Reload Systemd Daemon
Reload the systemd daemon to apply any changes made to system configurations, including the newly added entry in /etc/fstab.
```
systemctl daemon-reload
```






