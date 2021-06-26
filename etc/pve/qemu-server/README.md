# pfSense [ 20001.conf ]

## Configuration via SSH
```
yes | pkg update
yes | pkg install nano
```

## Configuration via Web Configurator

### System / Advanced / Admin Access
[Required: [ Services / Acme / Certificates ]](#services--acme--certificates)
```
-> webConfigurator
    -> SSL/TLS Certificate: pfsense.[REDACTED]
```
---
```
-> Secure Shell
    -> Secure Shell Server: ☑ Enable Secure Shell
```

### System / Advanced / Networking
[Reason: [ When using VirtIO interfaces in Proxmox VE, hardware checksums must be disabled ]](https://docs.netgate.com/pfsense/en/latest/recipes/virtualize-proxmox-ve.html#disable-hardware-checksums-with-proxmox-ve-virtio)
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

### Services / Acme / Certificates
[Required: [ Services / Acme / Accountkeys ]](#services--acme--accountkeys)
```
-> ➕ Add
    -> Edit Certificate options
        -> Name: pfsense.[REDACTED]
        -> Description: [REDACTED]
        -> Status: Active
        -> Acme Account: [REDACTED]
        -> Private Key: 4096-bit RSA
        -> Domain SAN list
            -> Table
                -> Mode: Enabled
                -> Domainname: pfsense.[REDACTED]
                -> Method: DNS-GoDaddy
                    -> Key: [REDACTED]
                    -> Secret: [REDACTED]
                    -> Enable DNS alias mode: pfsense.[REDACTED]
        -> Actions list
            -> ➕ Add
                -> Table
                    -> Mode: Enabled
                    -> Command: /etc/rc.restart_webgui
                    -> Method: Shell Command
    -> 💾 Save
```

### Services / Acme / Accountkeys
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
