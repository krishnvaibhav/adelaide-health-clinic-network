# Security design

## ACLs

Every list permits ICMP before any deny rule, so that connectivity can be tested end to end. Router ACLs are stateless: `established` matches TCP packets with the ACK or RST flag set.

### STAFF-IN (inbound on the clinical, admin and staff Wi-Fi gateways, at every site)
```
permit icmp any any
deny   ip any 10.0.4.96 0.0.0.31     ! HQ management
deny   ip any 10.0.20.96 0.0.0.31    ! Norwood management
deny   ip any 10.0.36.96 0.0.0.31    ! Mawson Lakes management
permit ip any any
```

### GUEST-IN (inbound on the guest Wi-Fi gateways, at every site)
```
permit udp any any eq bootps         ! DHCP renewals to the gateway
permit icmp any any
deny   ip any 10.0.0.0 0.0.63.255    ! everything internal
permit ip any any                    ! internet
```

### DMZ-IN (inbound on R-HQ-EDGE Gi0/2)
```
permit icmp any any
permit tcp 10.0.4.64 0.0.0.15 10.0.0.0 0.0.63.255 established   ! replies only
deny   ip any 10.0.0.0 0.0.63.255
permit ip any any                                                ! e.g. outbound mail
```

### OUTSIDE-IN (inbound on R-HQ-EDGE Gi0/0, the internet side)
```
permit tcp any host 203.115.0.3 eq www
permit tcp any host 203.115.0.3 eq 443
permit tcp any host 203.115.0.4 eq smtp
permit tcp any host 203.115.0.4 eq pop3
permit tcp any any established       ! replies to inside users
permit udp any eq domain any         ! DNS replies
permit icmp any any
! implicit deny
```

### MGMT-ONLY (VTY access-class on every company router and switch)
```
permit 10.0.4.96 0.0.0.31
permit 10.0.20.96 0.0.0.31
permit 10.0.36.96 0.0.0.31
```

## Device hardening

- `enable secret` and `service password-encryption`
- SSH version 2 with a local user, `transport input ssh` (telnet disabled)
- `no ip domain-lookup`
- Trunks use native VLAN 999 and allowed-VLAN lists
- Unused switch ports are shut down and placed in VLAN 998
- The DMZ switch has no IP address and is managed by console only

The lab used the single password required by the assignment. Production would use unique, strong credentials and centralised AAA.
