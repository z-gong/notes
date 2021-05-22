## Preparation

#### Configure external and internal network of front node

External network `dhcp, ONBOOT=yes`
Internal network `static ip, netmask, ONBOOT=yes`

## Install xcat

#### Generate and modify xcat database - networks, site, passwd

`makenetworks` initialize networks table
`tabedit networks` keep only the line of internal network. Disable DHCP dynamic range. Add DHCP static range
`tabedit site` set domain and dhcpinterfaces
`tabedit passwd` set root password for compute node

#### Configure necessary services for internal network

`makentp` setup time server
`makedhcp -n` setup DHCP server
`makedns -n` setup DNS server

Enable internal network services
```
systemctl enable ntpd
systemctl enable dhcpd
systemctl enable named
```

#### Create osimage

```
copycds CentOS-xxxx.iso
lsdef -t osimage
```

## Install node

#### Add one node manually based on MAC

```
mkdef -t node cn-2-1 groups=all,queue netboot=pxe ip=10.1.2.1 mac=xx:xx:xx:xx:xx:xx
```

#### Update internal network information

`makehosts` update `/etc/hosts` file
`makedhcp -a` assign static IP in `dhcpd.leases`
Run `makedhcp -q all` to check if static IP are correct assigned
If necessary, run `makedhcp -d -a` to delete current static IP in `dhcpd.leases`
`makedns -n` update DNS lookup database

#### Install OS on node

```
nodeset cn-2-1 osimage=centos7.9-x86_64-install-compute
```
Then manually boot cn-2-1

## Post installation

#### Install extra packages

Put a custom script `disable-repo` under `/install/postscripts/custom`
Execute `updatenode all custom/disable-repo` to disable the yum repo `CentOS-Base.repo`

Execute `lsdef -t osimage` to get the file for `pkglist`, and add necessary packages into it.
Execute `updatenode all ospkgs` to install these packages.

### Install munge, slurm and Nvidia driver

Execute `lsdef -t osimage` to get the location for other packages.
Put rpm files into the directory, and execute `createrepo .` to build repository.
Specify the `pkglist` by executing
`chdef -t osimage -o centos7.9-x86_64-install-compute  otherpkglist=/install/post/otherpkgs/otherpkgs.pkglist`
Put `munge, slurm-slurmd, slurm-pmi, kmod-nvidia, nvidia-x11-drv` into `otherpkgs.pkglist`
Execute `updatenode all otherpkgs` to install these rpms.

#### Sync config files

Specify the `synclist` by executing
`chdef -t osimage -o centos7.9-x86_64-install-compute synclists=/install/post/syncfiles/synclist`

Put `auto.master`, `auto.xcatmn`, `munge.key` into `synclist`
Execute `updatenode all -F` to sync these files.

#### Enable services and fix permissions

Execute `updatenode all custom/fix-service` to enable `autofs` and fix the permissions of `munge` and `slurm`
Execute `psh all systemctl restart slurmd`

## Configure firewall and fail2ban

`systemctl enable firewalld`
`systemctl start firewalld`
Allow all connections through internal network interface `em1` and `http` service from external network
`firewall-cmd --zone=trusted --permanent --add-interface=em1`
`firewall-cmd --zone=public --permanent --add-service=http`
`firewall-cmd --reload`

`yum --enablerepo=epel install fail2ban`
```
$ vim /etc/fail2ban/jail.local

[DEFAULT]
# Ban hosts for one day:
bantime = 86400
ignoreip = 202.120.51.74

[sshd]
enabled = true
```

`systemctl enable fail2ban`
`systemctl start fail2ban`
