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

### System / Advanced / Firewall & NAT
```
-> Bogon Networks
    -> Update Frequency: Weekly
       The frequency of updating the lists of IP addresses that are reserved (but not RFC 1918) or not yet assigned by IANA.
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
    -> DNS Servers
        -> 1.1.1.1
            -> one.one.one.one
        -> 8.8.4.4
           Address
           Enter IP addresses to be used by the system for DNS resolution. These are also used for the DHCP service, DNS Forwarder and DNS Resolver when it has DNS Query Forwarding enabled.
            -> dns.google
               Hostname
               Enter the DNS Server Hostname for TLS Verification in the DNS Resolver (optional).
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

### System / Routing / Gateways
```
-> Default gateway
    -> Default gateway IPv4: WAN_DHCP
```

### System / User Manager / Groups
[Reason: [ The same (LDAP) groups must exist locally ]](https://docs.netgate.com/pfsense/en/latest/usermanager/ldap.html#ldap-groups)
```
-> Groups
    -> ➕ Add
        -> Group Properties
            -> Group name: administrator
            -> Scope: Remote
            -> Description: Administrator Service
        -> Assigned Privileges
            -> ➕ Add
                -> Group Privileges
                    -> Assigned privileges: WebCfg - All pages
                -> 💾 Save
        -> 💾 Save
    -> ➕ Add
        -> Group Properties
            -> Group name: vpn
            -> Scope: Remote
            -> Description: VPN Service
        -> 💾 Save
```

### System / User Manager / Settings
[Required: [ System / User Manager / Authentication Servers ]](#system--user-manager--authentication-servers)
```
-> Authentication Server: ldap.[REDACTED]
-> Auth Refresh Time: 3600
```

### System / User Manager / Authentication Servers
```
-> ➕ Add
    -> Server Settings
        -> Descriptive name: ldap.[REDACTED]
        -> Type: LDAP
    -> LDAP Server Settings
        -> Hostname or IP address: ldap.[REDACTED]
           NOTE: When using SSL/TLS or STARTTLS, this hostname MUST match a Subject Alternative Name (SAN) or the Common Name (CN) of the LDAP server SSL/TLS Certificate.
        -> Port value: 636
        -> Transport: SSL/TLS Encrypted
        -> Peer Certificate Authority: Global Root CA List
           This CA is used to validate the LDAP server certificate when 'SSL/TLS Encrypted' or 'STARTTLS Encrypted' Transport is active. This CA must match the CA used by the LDAP server.
        -> Search scope
            -> Level: Entire Subtree
            -> Base: dc=services,[REDACTED]
        -> Authentication containers: dc=people,[REDACTED]
           Note: Semi-Colon separated. This will be prepended to the search base dn above or the full container path can be specified containing a dc= component.
Example: CN=Users;DC=example,DC=com or OU=Staff;OU=Freelancers
        -> Bind anonymous: ☐ Use anonymous binds to resolve distinguished names
        -> Bind credentials
            -> cn=[REDACTED],[REDACTED]
                -> [REDACTED]
        -> Group member attribute: memberOf
        -> Group Object Class: groupOfNames
        -> Allow unauthenticated bind: ☐ Allow unauthenticated bind
```

### Interfaces / Interface Assignments
[Required: [ VPN / OpenVPN / Servers ]](#vpn--openvpn--servers)
```
-> Available network ports: ovpns1 ([REDACTED] Zone Link Server)
    -> ➕ Add
-> 💾 Save
```

### Interfaces / OPT1 (ovpns1)
[Required: [ Interfaces / Interface Assignments ]](#interfaces--interface-assignments)
```
-> General Configuration
    -> Enable: ☑ Enable interface
    -> Description: VPN[REDACTED]ZLS
-> Reserved Networks
    -> Block bogon networks
        -> ☑
           Blocks traffic from reserved IP addresses (but not RFC 1918) or not yet assigned by IANA. Bogons are prefixes that should never appear in the Internet routing table, and so should not appear as the source address in any packets received.
           This option should only be used on external interfaces (WANs), it is not necessary on local interfaces and it can potentially block required local traffic.
            Note: The update frequency can be changed under System > Advanced, Firewall & NAT settings.
-> 💾 Save
```

### Firewall / NAT / Port Forward
```
-> Rules
    -> ⮯ Add
        -> Edit Redirect Entry
            -> Protocol: TCP/UDP
            -> Destination port range
                -> DNS
                   From port
                -> DNS
                   To port
            -> Redirect target IP
                -> [REDACTED].10[REDACTED]
                   Address
            -> Redirect target port
                -> DNS
                   Port
            -> Description: DNS Port Forward to Cluster
        -> 💾 Save
```
    
### Firewall / NAT / Outbound
[Reason: [ OpenVPN ]](#)
```
-> Outbound NAT Mode
    -> Mode: ◉
            Hybrid Outbound NAT rule generation.
            (Automatic Outbound NAT + rules below)
        -> 💾 Save
```

### Firewall / Rules / WAN
```
-> Rules (Drag to Change Order)
    -> ⮯ Add
        -> Edit Firewall Rule
            -> Action: Pass
            -> Protocol: UDP
        -> Destination
            -> Destination: WAN Address
            -> Destination Port Range
                -> [REDACTED]
                   From
                -> [REDACTED]
                   To
        -> Description: Pass from WAN to [REDACTED] Zone Link Server
        -> 💾 Save
```

### Firewall / Rules / VPN[REDACTED]ZLS
[Required: [ Interfaces / OPT1 (ovpns1) ]](#interfaces--opt1-ovpns1)
```
-> Rules (Drag to Change Order)
    -> ⮯ Add
        -> Edit Firewall Rule
            -> Action: Pass
            -> Protocol: Any
        -> Source
            -> Source: VPN[REDACTED]ZLS net
        -> Destination
            -> Destination: LAN Net
        -> Description: Pass from [REDACTED] Zone Link Clients to LAN
        -> 💾 Save
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

### Services / Auto Configuration Backup / Settings
```
-> Auto Config Backup
    -> Enable ACB: ☑ Enable automatic configuration backups
    -> Encryption Password
        -> [REDACTED]
        -> [REDACTED]
           Confirm
    -> Hint/Identifier: pfsense.[REDACTED]
-> 💾 Save
```

### Services / DHCP Server / LAN
```
-> General Options
    -> Range
        -> [REDACTED].20
           From
        -> [REDACTED].99
           To
-> Other Options
    -> Network Booting: ⚙ Display Advanced
        -> Enabled: ☑ Enables network booting
        -> Next Server: [REDACTED].1
        -> Default BIOS file name: undionly-to-matchbox.kpxe
-> DHCP Static Mappings for this Interface    
    -> ➕ Add
        -> Static DHCP Mapping on LAN
            -> MAC Address: 62:37:9F:80:F7:17
            -> IP Address: [REDACTED].1
            -> Hostname: pfsense
            -> Description: pfsense LAN Interface
            -> ARP Table Static Entry: ☑ Create an ARP Table Static Entry for this MAC & IP Address pair.
            -> Domain name: [REDACTED]
        -> 💾 Save
```
#### Repeat:
```
-> DHCP Static Mappings for this Interface 
    -> ➕ Add
        -> Static DHCP Mapping on LAN
            -> MAC Address: [REDACTED]
            -> IP Address: [REDACTED].10[REDACTED]
            -> Hostname: [REDACTED]
            -> Description: [REDACTED] LAN Interface
            -> ARP Table Static Entry: ☑ Create an ARP Table Static Entry for this MAC & IP Address pair.
            -> Domain name: [REDACTED]
        -> 💾 Save
```

### VPN / OpenVPN / Servers
```
-> OpenVPN Servers
    -> ➕ Add
        -> General Information
            -> Server mode: Remote Access ( SSL/TLS )
            -> Local port: [REDACTED]
            -> Description: [REDACTED] Zone Link Server
        -> Cryptographic Settings
            -> TLS Configuration
                -> ☑ Use a TLS Key
                -> ☐ Automatically generate a TLS Key.
            -> TLS Key
                -> [REDACTED]
                   Paste the TLS key here.
                   This key is used to sign control channel packets with an HMAC signature for authentication when establishing the tunnel. 
            -> Peer Certificate Authority: [REDACTED] CA
            -> Server certificate: [REDACTED] Server
            -> Fallback Data Encryption Algorithm: AES-256-GCM (256 bit key, 128 bit block)
        -> Tunnel Settings
            -> IPv4 Tunnel Network: [REDACTED]
            -> IPv4 Local network(s): [REDACTED]
        -> Client Settings
            -> Dynamic IP: ☑ Allow connected clients to retain their connections if their IP address changes.
        -> 💾 Save
```
