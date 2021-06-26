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

### System / General Setup
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
        -> acme: ➕ Install
        -> nmap: ➕ Install
        -> openvpn-client-export: ➕ Install
        -> qemu-guest-agent: ➕ Install [ ≥ 2.6.? ONLY ]
        -> sudo: ➕ Install
```

### Services / Acme / Settings
```
-> Cron Entry: ☑ Enable Acme client renewal job. This will configure cron to renew certificates once a day at 3:16. Keeping track of the last successful renewal and the number of days set after to renew again. When renewal happens a service can be restarted or a shell script run to load the new certificate for services that need it, if needed this needs to be configured as a action under the certificate settings.
-> Write Certificates: ☑ Write ACME certificates to /conf/acme/ in various formats for use by other scripts or daemons which do not integrate with the certificate manager.
```

### Services / Acme/ Accountkeys
```
-> ➕ Add
    -> Edit Certificate options
        -> Name: [REDACTED]
        -> Description: [REDACTED]
        -> ACME Server: Let's Encrypt Production ACME v2 (Applies rate limits to certificate requests)
        -> E-Mail Address: [REDACTED]
        -> Account key: [REDACTED]
    -> 💾 Save
```
