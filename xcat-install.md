### set external network

dhcp, ONBOOT=yes

### set internal network

static ip, netmask, ONBOOT=yes

### generate and modify xcat table networks, site, passwd

makenetworks

tabedit networks

tabedit site

tabedit passwd

### config services

makentp

makedhcp -n

makedns -n

systemctl enable ntpd

systemctl restart ntpd

systemctl enable dhcpd

systemctl restart dhcpd

systemctl enable named

systemctl restart named

### generate osimage

copycds CentOS-xxxx.iso

lsdef -t osimage

### generate netboot image

mknb x86_64

### add node manually

mkdef -t node cn1 groups=all netboot=pxe ip=10.1.1.101 mac=xx​:xx:​xx​:xx:​xx:xx

### update network information

makehosts

makedhcp -a

makedns -n

### set netboot

nodeset cn1 osimage=centos7.6-x86_64-install-compute

### manually boot cn1