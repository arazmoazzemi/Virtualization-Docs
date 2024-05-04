https://forum.proxmox.com/threads/cluster-wide-networking-management-with-sdn.137279/

192.168.200.61 pve01.mylab.loc pve01
192.168.200.62 pve02.mylab.loc pve02
192.168.200.63 pve03.mylab.loc pve03
192.168.200.65 share
192.168.200.70 bps 



Create a file at /etc/apt/sources.list.d/pve-no-enterprise.list with the following contents:

# not for production use
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription


auto enp0s3
iface enp0s3 inet static
address 192.168.18.22          
netmask 255.255.255.0           
gateway 192.168.18.22           
dns-nameservers 8.8.4.4 8.8.8.8


apt install nfs-kernel-server -y
systemctl start nfs-kernel-server
systemctl enable nfs-kernel-server


systemctl status nfs-kernel-server

mkdir -p /home/ctshare

chown nobody:nogroup /home/ctshare

chmod 755 /home/ctshare


nano /etc/exports


/home/ctshare 192.168.1.0/24(rw,sync,no_subtree_check)

exportfs -a



ufw allow from 192.168.200.0/24 to any port nfs
ufw reload



pveam update
