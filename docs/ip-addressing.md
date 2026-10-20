# IP addressing plan

The allocated block `10.0.0.0/18` is split into four /20 blocks so each site is reachable through one summarised route. Inside each site the third octet follows the same pattern: +0 clinical, +1 admin, +2 staff Wi-Fi, +3 guest Wi-Fi, +4 printers and management, +5 voice (reserved).

## Site blocks

| Block | Site | Spare for growth |
|---|---|---|
| 10.0.0.0/20 | Head office | 10.0.6.0 – 10.0.15.255 |
| 10.0.16.0/20 | Norwood | 10.0.22.0 – 10.0.31.255 |
| 10.0.32.0/20 | Mawson Lakes | 10.0.38.0 – 10.0.47.255 |
| 10.0.48.0/20 | WAN links, loopbacks, fourth clinic (10.0.56.0/21) | 10.0.50.0/23, 10.0.52.0/22 |

## VLANs and subnets

| VLAN | Head office | Norwood | Mawson Lakes |
|---|---|---|---|
| 10 Clinical | 10.0.0.0/24 | 10.0.16.0/24 | 10.0.32.0/24 |
| 20 Administration | 10.0.1.0/24 | 10.0.17.0/24 | 10.0.33.0/24 |
| 40 Staff Wi-Fi | 10.0.2.0/24 | 10.0.18.0/24 | 10.0.34.0/24 |
| 50 Guest Wi-Fi | 10.0.3.0/24 | 10.0.19.0/24 | 10.0.35.0/24 |
| 60 Printers | 10.0.4.0/27 | 10.0.20.0/27 | 10.0.36.0/27 |
| 80 Servers | 10.0.4.32/27 | – | – |
| 90 DMZ | 10.0.4.64/28 (gateway on the edge router) | – | – |
| 99 Management | 10.0.4.96/27 | 10.0.20.96/27 | 10.0.36.96/27 |
| 70 Voice (reserved) | 10.0.5.0/24 | 10.0.21.0/24 | 10.0.37.0/24 |

The gateway is the first usable address, except management (`.97`) and servers (`.33`).

## Infrastructure links

| Link | Subnet |
|---|---|
| SW-HQ-CORE Gi1/0/2 – R-NOR Gi0/0 | 10.0.48.0/30 |
| SW-HQ-CORE Gi1/0/3 – R-MAW Gi0/0 | 10.0.48.4/30 |
| R-NOR – R-MAW backup (reserved) | 10.0.48.8/30 |
| R-HQ-EDGE Gi0/1 – SW-HQ-CORE Gi1/0/1 | 10.0.48.12/30 |
| Router loopbacks | 10.0.49.1–3/32 |

## Public addresses (203.115.0.0/28)

| Address | Use |
|---|---|
| 203.115.0.1 | ISP gateway |
| 203.115.0.2 | Edge router (PAT for all internal users) |
| 203.115.0.3 | Web server (static NAT to 10.0.4.66) |
| 203.115.0.4 | Mail server (static NAT to 10.0.4.67) |

`198.51.100.0/30` (an RFC 5737 documentation range) is used only for the simulated internet.
