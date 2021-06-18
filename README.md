# proxmox
Proxmox config files for/and Windows VM with working NVidia Graphics card passthrough.

After modifications to /etc/default/grub and /etc/modprobe.d/passthrough.conf, run:
update-initramfs -u -k all
