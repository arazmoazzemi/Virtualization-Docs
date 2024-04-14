proxmox-backup-manager datastore remove share

systemctl restart proxmox-backup.service
systemctl restart proxmox-backup-proxy.service


systemctl disable mnt-datastore-<id>.mount
systemd-escape <my-id>
rm /etc/systemd/system/<id>

--------------------------------------------------------------------------------------------------


https://www.derekseaman.com/2023/04/how-to-setup-synology-nfs-for-proxmox-backup-server-datastore.html



bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pbs-install.sh)"

apt update
apt install qemu-guest-agent -y


mkdri share
chown backup:backup /share
chmod 775 /share


# chmod -R 775 /mnt/synology/.chunks

echo "192.168.31.99:/volume2/pbs /share nfs vers=3,nouser,atime,auto,retrans=2,rw,dev,exec 0 0" >> /etc/fstab

mount -a

systemctl daemon-reload

chmod 775 /share







