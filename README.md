#Hospital Enterprise Network: Addressing Plan

Matches the final design: gateways on the distribution layer, separate DHCP server, dual-ISP edge with an outside HSRP segment.


Tabs
VLAN Addressing: 12 VLANs with HSRP gateways, SVIs, active switch and DHCP pool bounds
DHCP Pools: what to enter on DHCP-SRV, linked to VLAN Addressing
Static Hosts: servers, WLC, access switch management and WLC dynamic interfaces
Infra P2P Links: routed links between core, distribution and firewall
Outside Segment: the shared /29 for the edge routers and the ASA
Loopbacks, WAN Links, VPN Overlay, Branch Clinic, Cabling, CIDR Reference


Conventions
Gateway VIP is the first usable address. DIST1 SVI is the next address, DIST2 SVI the one after.
The HSRP active switch is also the spanning-tree root for that VLAN, split between DIST1 and DIST2.
Servers and infrastructure are static. Only user, wireless, voice and AP VLANs use DHCP.
Blue text = design value. Black = formula. Green = pulled from another tab.

Address space
10.10.0.0/16 main site, 10.20.0.0/16 branch clinic, 10.99.99.0/30 VPN tunnel
203.0.113.0/24, 198.51.100.0/24, 192.0.2.0/24 WAN and internet simulation (RFC 5737)
Free in the main site: 10.10.5.192/26, then 10.10.6.0 onward. Free infrastructure links from 10.10.254.40.
