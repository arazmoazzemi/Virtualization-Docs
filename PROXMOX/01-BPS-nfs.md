### Configure NFS Share for Synology in PBS Server:

### [Help lnk](https://www.derekseaman.com/2023/04/how-to-setup-synology-nfs-for-proxmox-backup-server-datastore.html)
### To enable NFS (Network File System) sharing between your PBS (Proxmox Backup Server) and Synology NAS, follow these steps:

1. Run Post-Installation Script
Execute the provided script to set up necessary configurations for the PBS server.
```
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pbs-install.sh)"
```

2. Update Package Manager
Ensure your package manager is up to date.
```
apt update
```

3. Install QEMU Guest Agent
Install the QEMU Guest Agent to facilitate communication between the PBS server and its virtual machines.
```
apt install qemu-guest-agent -y
```


4. Create NFS Share Directory
Create a directory named share to serve as the mount point for the NFS share.
```
mkdrir /share
```

5. Set Ownership and Permissions
Set the ownership of the share directory to the backup user and group and adjust permissions.
```
chown backup:backup share/
chmod 775 share/
```

```
# chmod -R 775 /mnt/synology/.chunks
```

6. Mount the NFS Share
Add an entry to /etc/fstab to automatically mount the NFS share from the Synology NAS upon system boot.
```
echo "192.168.31.99:/volume2/pbs /share nfs vers=3,nouser,atime,auto,retrans=2,rw,dev,exec 0 0" >> /etc/fstab
```

7. Mount All Filesystems
Mount all filesystems specified in /etc/fstab, including the newly added NFS share.
```
mount -a
```

8. Reload Systemd Daemon
Reload the systemd daemon to apply any changes made to system configurations.
```
systemctl daemon-reload
```

9. Adjust Permissions (Optional)
If necessary, adjust permissions again for the share directory.
```
chmod 775 share/
```






