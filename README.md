# proxmox
Proxmox config files for/and Windows VM with working NVidia Graphics card passthrough.

After modifications to /etc/default/grub and /etc/modprobe.d/passthrough.conf, run:
```bash
update-initramfs -u -k all
```

## Personal Initial Instalation ##
```
-> Options
  -> Filesystem
      zfs (RAID1)
  -> Disk Setup
    -> Harddisk 0
        /dev/ds[REDACTED] ([REDACTED]GiB, [REDACTED] [REDACTED]-7)
    -> Harddisk 1
        /dev/ds[REDACTED] ([REDACTED]GiB, [REDACTED] [REDACTED]-0)
    -> Harddisk 2
        -- do not use --
    -> Harddisk 3
        -- do not use --
    -> Harddisk 4
        -- do not use --
```
```
-> Country
  [REDACTED]
-> Time zone
  [REDACTED]
-> Keyboard Layout
  [REDACTED]
```
```
-> Password
  [REDACTED]
-> Confirm
  [REDACTED]
-> Email
  [REDACTED]
```
```
-> Mangement Interface:
  [REDACTED]8
-> Hostname (FQDN):
  proxmox[REDACTED]
-> IP Address (CIDR)
  [REDACTED]49 / [REDACTED]
-> Gateway:
  [REDACTED]
-> DNS Server:
  [REDACTED]
```

### Disable Enterprise Repository
```bash
mv /etc/apt/sources.list.d/pve-enterprise.list /etc/apt/sources.list.d/pve-enterprise.list.disabled
```

### Enable No Subscription Repository
```bash
echo 'deb http://download.proxmox.com/debian/pve buster pve-no-subscription' > /etc/apt/sources.list.d/pve-no-subscription.list
```

### Enable Ceph Repository
```bash
echo 'deb http://download.proxmox.com/debian/ceph-octopus buster main' > /etc/apt/sources.list.d/ceph.list
```

### Set [ Email from address ]
```
-> Datacenter
  -> Options
    -> Email from address
      [REDACTED]@[REDACTED]
```

### Set [ MAC address prefix ]
```
-> Datacenter
  -> Options
    -> MAC address prefix
      [ANY OF: x2:xx:xx: x6:xx:xx: xA:xx:xx: xE:xx:xx: ]
```

### Disable Subscription Nag Screen
```bash
sed -i.backup -z "s/res === null || res === undefined || \!res || res\n\t\t\t.data.status.toLowerCase() \!== 'active'/false/g" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js && systemctl restart pveproxy.service
```
