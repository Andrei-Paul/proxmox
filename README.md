# proxmox
Proxmox config files for/and Windows VM with working NVidia Graphics card passthrough.

After modifications to /etc/default/grub and /etc/modprobe.d/passthrough.conf, run:
```bash
update-initramfs -u -k all
```

**Personal Initial Instalation**
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
