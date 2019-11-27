### Configure external network

```
dhcp, ONBOOT=yes
```

### Configure internal network

```
static ip, netmask, ONBOOT=yes
```

### Generate and modify xcat table - networks, site, passwd

```
makenetworks
tabedit networks
tabedit site
tabedit passwd
```

### Configure neccesary services for internal network

```
makentp
makedhcp -n
makedns -n
systemctl enable ntpd
systemctl restart ntpd
systemctl enable dhcpd
systemctl restart dhcpd
systemctl enable named
systemctl restart named
```

### Create osimage

```
copycds CentOS-xxxx.iso
lsdef -t osimage
```

### Generate netboot image

```
mknb x86_64
```

### Add nodes and assign the internal IP manually

```
mkdef -t node cn1 groups=all netboot=pxe ip=10.1.1.101 mac=xx:xx:xx:xx:xx:xx
```

### Update internal network information

```
makehosts
makedhcp -a
makedns -n
```

### Let node install OS during next boot

```
nodeset cn1 osimage=centos7.6-x86_64-install-compute
```

### Manually boot cn1