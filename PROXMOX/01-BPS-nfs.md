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


-------------------------------------------------------------------------------------------------------

Connect the iSCSI target in your PBS server

iqn.2000-01.com.synology:save.Target-1.3bee535fbe

nano /etc/iscsi/iscsid.conf


node.startup = automatic



iscsiadm -m discovery -t st -p 192.168.31.99
iscsiadm -m node --login
iscsiadm -m session -o show

lsblk 
mkdir btrfs
mkfs.btrfs /dev/sdb
mount /dev/sdb btrfs/
df -h

echo "/dev/sdb /root/btrfs btrfs defaults 0 0" >> /etc/fstab

chmod -R 777 /mnt/synology/.chunks

journalctl -xb


iscsiadm --mode node -T iqn.2000-01.com.synology:save.Target-1.3bee535fbe -p 192.168.31.99 -o update -n node.startup -v automatic
iscsiadm --mode node -T iqn.2000-01.com.synology:save.Target-1.3bee535fbe -p 192.168.31.99 -o update -n node.conn[0].startup -v automatic
sed -i '/^node.startup = /s/manual/automatic/g' /etc/iscsi/iscsid.conf

systemctl daemon-reload

-----------


iscsiadm -m discovery -t sendtargets -p 192.168.31.99
iscsiadm -m node --targetname "<target_name>" --portal "<target_ip_address>:<port>" --login

iscsiadm -m session




echo "- - -" > /sys/class/scsi_host/hostX/scan






