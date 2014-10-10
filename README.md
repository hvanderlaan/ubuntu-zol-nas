Ubuntu nas with zol (zfs on linux)
==================================
install ubuntu 14.04 LTS server x64 (default)
Update and upgrade ubuntu server
================================
```
sudo apt-get update 
sudo apt-get -y dist-upgrade
```
Installing extra packages
=========================
```
sudo apt-get -y install openssh-server vim curl git build-essentail
```
VMware Only: install vmware tools
=================================
```
install vmware tools (in vmware)
sudo mount -o loop /dev/cdrom /mount
cd /var/tmp
cp /mnt/VMware-tools-*.tar.gz .
tar zxvf VMware-tools-*.tar.gz
cd VMware-tools
sudo ./install-vmware-tools.pl
sudo shutdown -r now
```
Installing zol (zfs on linux)
=============================
```
sudo apt-add-repository --yes ppa:zfs-native/stable
sudo apt-get update
sudo apt-get -y install ubuntu-zfs
sudo modprobe zfs
```
Creating zfs raidz pool
=======================
```
sudo zpool create -f ubuntu-zol-nas /dev/sdb /dev/sdc /sev/sdd
sudo zpool status
```
Creating zfs datasets
=====================
```
sudo zfs create ubuntu-zol-nas/data
sudo zfs set mountpoint=none ubuntu-zol-nas/data
sudo zfs set quota=3T ubuntu-zol-nas/data

sudo zfs create ubuntu-zol-nas/data/movies
sudo zfs set mountpoint=/nas/movies ubuntu-zol-nas/data/movies
sudo zfs set quota=1T ubuntu-zol-nas/data/movies

sudo zfs create ubuntu-zol-nas/data/software
sudo zfs set mountpoint=/nas/software ubuntu-zol-nas/data/software
sudo zfs set quota=1T ubuntu-zol-nas/data/software

sudo zfs create ubuntu-zol-nas/data/pictures
sudo zfs set mountpoint=/nas/pictures ubuntu-zol-nas/data/pictures
sudo zfs set quota=500G ubuntu-zol-nas/data/pictures

sudo zfs create ubuntu-zol-nas/data/documents
sudo zfs set mountpoint=/nas/documents ubuntu-zol-nas/data/documents
sudo zfs set quota=200G ubuntu-zol-nas/data/documents
```
Installing nfs
==============
```
sudo apt-get -y install nfs-kernel-server
sudo vi /etc/exports
  /nas/movies  192.168.1.0/255.255.255.0(rw,no_subtree_check,no_root_squash)
  /nas/software  192.168.1.0/255.255.255.0(rw,no_subtree_check,no_root_squash)
  /nas/pictures  192.168.1.0/255.255.255.0(rw,no_subtree_check,no_root_squash)
  /nas/documents  192.168.1.0/255.255.255.0(rw,no_subtree_check,no_root_squash)
sudo service nfs-kernel-server restart
showmount -e
```
Installing samba
================
```
sudo apt-get -y install samba
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.backup
sudo cat << EOF > /etc/samba/smb.conf
[global]
        netbios name = NAS
        workgroup = WORKGROUP
        security = user

[movies]
        path = /nas/movies
        public = yes
        browseable = yes
        writeable = yes
        create mask = 0777
        directory mask = 0777

[software]
        path = /nas/software
        public = yes
        browseable = yes
        writeable = yes
        create mask = 0777
        directory mask = 0777

[pictures]
        path = /nas/pictures
        public = yes
        browseable = yes
        writeable = yes
        create mask = 0777
        directory mask = 0777

[documents]
        path = /nas/documents
        public = yes
        browseable = yes
        writeable = yes
        create mask = 0777
        directory mask = 0777
EOF

sudo service samba restart
```
Final reboot test
=================
```
sudo shutdown -r now
```
