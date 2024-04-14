### BPS nfs for synology:

### [Help lnk](https://www.derekseaman.com/2023/04/how-to-setup-synology-nfs-for-proxmox-backup-server-datastore.html)

```
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pbs-install.sh)"
```

```
apt update
apt install qemu-guest-agent -y
```

```
mkdri share
chown backup:backup /share
chmod 775 share/
```

```
# chmod -R 775 /mnt/synology/.chunks
```

```
echo "192.168.31.99:/volume2/pbs /share nfs vers=3,nouser,atime,auto,retrans=2,rw,dev,exec 0 0" >> /etc/fstab

mount -a

systemctl daemon-reload
chmod 775 share/
```






