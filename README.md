# Hospital Enterprise Network: VLSM Addressing Plan

A Cisco Packet Tracer project that models a hospital network with a main site, a remote branch clinic, redundant WAN uplinks, and a site-to-site IPsec VPN. This repository documents the full IP addressing scheme, built with VLSM so each segment gets a subnet sized to its needs.

The addressing plan is also available as a spreadsheet: [`Hospital_Network_VLSM_Addressing.xlsx`](Hospital_Network_VLSM_Addressing.xlsx). Subnet masks and usable host counts in the workbook are live formulas driven by the prefix length.

## Contents

- [Topology](#topology)
- [Address Space Summary](#address-space-summary)
- [Addressing Conventions](#addressing-conventions)
- [Main Site VLANs](#main-site-vlans)
- [Infrastructure Point-to-Point Links](#infrastructure-point-to-point-links)
- [Loopbacks](#loopbacks)
- [WAN Links](#wan-links)
- [VPN Overlay](#vpn-overlay)
- [Branch Clinic](#branch-clinic)
- [Growth Space](#growth-space)
- [Repository Layout](#repository-layout)

## Topology

<!-- Replace this with your own screenshot, for example docs/topology.png -->
![Network topology](docs/topology.png)

The design has these layers at the main site:

- Two core switches (Core1, Core2) and two distribution switches (Dist1, Dist2)
- A Cisco ASA firewall with inside links to both cores and outside links to two edge routers
- Two edge routers (Edge A, Edge B), each with its own ISP uplink
- A branch clinic router connected to the main site through an IPsec tunnel

## Address Space Summary

| Block | Purpose |
|---|---|
| 10.10.0.0/16 | Main hospital site |
| 10.20.0.0/16 | Remote branch clinic |
| 10.99.99.0/30 | Site-to-site VPN tunnel overlay |
| 203.0.113.0/24 | WAN links (RFC 5737 documentation range) |
| 198.51.100.0/24 | WAN links (RFC 5737 documentation range) |

The WAN links use RFC 5737 documentation ranges, so they are safe to use in a lab or portfolio project without overlapping real public addresses.

## Addressing Conventions

- **Gateway pattern:** `.1` is the HSRP virtual IP, which is the address hosts actually use as their default gateway.
- **Core SVIs:** Core switch 1 uses the gateway address plus 1, and Core switch 2 uses the gateway address plus 2. For VLAN 10, that gives `.1` as the VIP, `.2` on Core1, and `.3` on Core2.
- **DHCP pools** start at `.4` on the main site VLANs, leaving the low addresses free for the VIP and the two core SVIs.
- **Static-only VLANs:** EHR / Servers, Management, and Native carry no DHCP pool. Addresses are assigned manually.

## Main Site VLANs

There are 11 VLANs at the main site, all carved out of 10.10.0.0/16.

| VLAN | Name | Network | Prefix | Subnet Mask | Gateway (HSRP VIP) | DHCP Range | Broadcast | Usable Hosts |
|---|---|---|---|---|---|---|---|---|
| 10 | Administration | 10.10.5.0 | /26 | 255.255.255.192 | 10.10.5.1 | 10.10.5.4 - 10.10.5.62 | 10.10.5.63 | 62 |
| 20 | Doctors | 10.10.4.0 | /25 | 255.255.255.128 | 10.10.4.1 | 10.10.4.4 - 10.10.4.126 | 10.10.4.127 | 126 |
| 30 | Nurses | 10.10.1.0 | /24 | 255.255.255.0 | 10.10.1.1 | 10.10.1.4 - 10.10.1.254 | 10.10.1.255 | 254 |
| 40 | EHR / Servers | 10.10.5.96 | /27 | 255.255.255.224 | 10.10.5.97 | Static only | 10.10.5.127 | 30 |
| 50 | Radiology / PACS | 10.10.5.64 | /27 | 255.255.255.224 | 10.10.5.65 | 10.10.5.68 - 10.10.5.94 | 10.10.5.95 | 30 |
| 60 | Biomedical Devices | 10.10.0.0 | /24 | 255.255.255.0 | 10.10.0.1 | 10.10.0.4 - 10.10.0.254 | 10.10.0.255 | 254 |
| 70 | Staff Wi-Fi | 10.10.2.0 | /24 | 255.255.255.0 | 10.10.2.1 | 10.10.2.4 - 10.10.2.254 | 10.10.2.255 | 254 |
| 80 | Guest Wi-Fi | 10.10.4.128 | /25 | 255.255.255.128 | 10.10.4.129 | 10.10.4.132 - 10.10.4.254 | 10.10.4.255 | 126 |
| 90 | Voice / VoIP | 10.10.3.0 | /24 | 255.255.255.0 | 10.10.3.1 | 10.10.3.4 - 10.10.3.254 | 10.10.3.255 | 254 |
| 99 | Native (unused) | 10.10.5.160 | /28 | 255.255.255.240 | None | Reserved, no hosts | 10.10.5.175 | 14 |
| 100 | Management | 10.10.5.128 | /27 | 255.255.255.224 | 10.10.5.129 | Static only | 10.10.5.159 | 30 |

Notes on specific VLANs:

- **VLAN 40 (EHR / Servers):** no DHCP pool, servers are assigned statically.
- **VLAN 80 (Guest Wi-Fi):** isolated, with no access to internal VLANs.
- **VLAN 99 (Native):** the trunk native VLAN, kept empty for security.
- **VLAN 100 (Management):** used for infrastructure device management only.

## Infrastructure Point-to-Point Links

Each link between core, distribution, and firewall devices uses a /30 (255.255.255.252) taken from 10.10.254.0.

| Link | Subnet | Endpoint A | Endpoint A IP | Endpoint B | Endpoint B IP |
|---|---|---|---|---|---|
| Core1 <-> Core2 | 10.10.254.0/30 | Core switch 1 | 10.10.254.1 | Core switch 2 | 10.10.254.2 |
| Dist1 <-> Dist2 | 10.10.254.4/30 | Distribution SW1 | 10.10.254.5 | Distribution SW2 | 10.10.254.6 |
| Core1 <-> Dist1 | 10.10.254.8/30 | Core switch 1 | 10.10.254.9 | Distribution SW1 | 10.10.254.10 |
| Core2 <-> Dist2 | 10.10.254.12/30 | Core switch 2 | 10.10.254.13 | Distribution SW2 | 10.10.254.14 |
| ASA inside <-> Core1 | 10.10.254.16/30 | ASA (inside) | 10.10.254.17 | Core switch 1 | 10.10.254.18 |
| ASA inside <-> Core2 | 10.10.254.20/30 | ASA (inside) | 10.10.254.21 | Core switch 2 | 10.10.254.22 |
| ASA outside <-> Edge A | 10.10.254.24/30 | ASA (outside) | 10.10.254.25 | Edge router A | 10.10.254.26 |
| ASA outside <-> Edge B | 10.10.254.28/30 | ASA (outside) | 10.10.254.29 | Edge router B | 10.10.254.30 |

## Loopbacks

Each device has a /32 loopback used as its router ID for OSPF and BGP.

| Device | Loopback IP | Notes |
|---|---|---|
| Core switch 1 | 10.10.255.1/32 | OSPF / BGP router ID |
| Core switch 2 | 10.10.255.2/32 | OSPF / BGP router ID |
| Distribution SW1 | 10.10.255.3/32 | |
| Distribution SW2 | 10.10.255.4/32 | |
| Edge router A | 10.10.255.5/32 | |
| Edge router B | 10.10.255.6/32 | |
| ASA (management) | 10.10.255.7/32 | Management only, not routed to the internet |

## WAN Links

| Link | Subnet | Subnet Mask | Notes |
|---|---|---|---|
| Edge router A <-> ISP A | 203.0.113.0/30 | 255.255.255.252 | Primary path |
| Edge router B <-> ISP B | 198.51.100.0/30 | 255.255.255.252 | Secondary / backup path |
| Branch clinic router <-> ISP | 203.0.113.4/30 | 255.255.255.252 | Independent uplink for the branch clinic, a non-overlapping /30 in the same documentation block |

## VPN Overlay

| Tunnel | Subnet | Endpoint A | Endpoint A IP | Endpoint B | Endpoint B IP |
|---|---|---|---|---|---|
| Site-to-site IPsec | 10.99.99.0/30 | Edge router A | 10.99.99.1 | Branch clinic router | 10.99.99.2 |

The tunnel network is kept separate from the LAN addressing of either site.

## Branch Clinic

The branch clinic uses 10.20.0.0/16, of which the first 10.20.0.0/24 is allocated so far.

| Segment | Network | Prefix | Subnet Mask | Gateway | DHCP Range | Usable Hosts |
|---|---|---|---|---|---|---|
| Clinic staff (doctors + nurses) | 10.20.0.0 | /26 | 255.255.255.192 | 10.20.0.1 | 10.20.0.2 - 10.20.0.62 | 62 |
| Clinic Wi-Fi | 10.20.0.64 | /26 | 255.255.255.192 | 10.20.0.65 | 10.20.0.66 - 10.20.0.126 | 62 |
| Clinic biomedical devices | 10.20.0.128 | /27 | 255.255.255.224 | 10.20.0.129 | 10.20.0.130 - 10.20.0.158 | 30 |
| Clinic management | 10.20.0.160 | /28 | 255.255.255.240 | None | Reserved, no hosts | 14 |

Clinic management is assigned statically.

## Growth Space

Space is left free in the main site block for future subnets:

- 10.10.5.176/28 through the rest of 10.10.5.0/24
- 10.10.6.0/23 through 10.10.253.0/24

## Repository Layout

```
.
├── README.md
├── Hospital_Network.pkt                    # Packet Tracer project file
└── docs/
    └── topology.png                        # Topology screenshot
```

## Tools

- Cisco Packet Tracer
