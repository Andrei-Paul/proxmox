# pfSense [ 20001.conf ]

## Configuration

### System / Advanced / Admin Access
```
-> Secure Shell
    -> Secure Shell Server: ☑ Enable Secure Shell
```

### System / Advanced / Networking
[ https://docs.netgate.com/pfsense/en/latest/recipes/virtualize-proxmox-ve.html#disable-hardware-checksums-with-proxmox-ve-virtio ]
```
-> Network Interfaces
    -> Hardware Checksum Offloading: ☑ Disable hardware checksum offload
```

#### System / General Setup
```
-> DNS Server Settings
    -> DNS Server Override: ☐ Allow DNS server list to be overridden by DHCP/PPP on WAN
-> webConfigurator
    -> Top Navigation: Fixed (Remains visible at top of page)
-> Associated Panels Show/Hide
    -> ☑ Available Widgets
       Show the Available Widgets panel on the Dashboard.
```

### System / Package Manager / Available Packages
```
-> Available Packages
    -> Packages
        -> acme: +Install
        -> nmap: +Install
        -> openvpn-client-export: +Install
        -> qemu-guest-agent: +Install [ ≥ 2.6.? ONLY ]
        -> sudo: +Install
```
