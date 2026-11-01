Wireless LAN Controller and Lightweight AP Lab (Cisco Packet Tracer)

A hands-on lab that configures a Cisco Wireless LAN Controller (WLC) and lightweight access points (LAPs) in Cisco Packet Tracer. The network uses DHCP-based controller discovery and department-specific WPA2 SSIDs mapped to AP Groups.

Overview

In a controller-based wireless design, lightweight access points do not hold their own configuration. They find a central WLC, join it, and receive their settings from it. This lab builds that setup from scratch and then segments wireless access by department using multiple SSIDs and AP Groups.

Objectives
Build a controller-based wireless topology in Packet Tracer.
Configure static management addressing and a DHCP pool that lets APs discover the WLC.
Complete the initial WLC setup through its HTTPS web interface.
Create multiple WPA2-secured SSIDs and map them to AP Groups.
Verify the design by connecting wireless clients and troubleshooting issues.
Topology
Device	Model	Role
Wireless LAN Controller	Cisco 2504	Central management of all APs
Switch	Cisco 2960	Connects the WLC, APs, DHCP server, and PC
Access points	LAP-PT	Lightweight APs managed by the WLC
DHCP server	Server-PT	Assigns IP addresses to APs and the WLC option
Management PC	PC-PT	Accesses the WLC web interface
Wireless clients	Smartphones	Used to test each SSID

Topology

Environment
Cisco Packet Tracer, version
No other software required
Configuration Summary
1. IP addressing and DHCP
Static management IP addresses were assigned to the WLC, the management PC, and the DHCP server.
A DHCP pool was created with the WLC address set as a DHCP option, so each access point can learn where the controller is and join it automatically.
2. Initial WLC setup
Opened the WLC management page from the PC browser over HTTPS.
Created the admin account and set the basic system parameters.
Defined the first SSID and its security key.
3. WLANs and AP Groups

Four WLANs were created, each secured with WPA2:

SSID	   Purpose
IT	     IT department
HR	     HR department
Finance	 Finance department
Guest	   Guest access

Access points were placed into AP Groups, and each SSID was mapped to the matching group. This controls which networks are broadcast by which access points.

4. Testing
Connected wireless clients to each SSID and confirmed they received an address and could join the network.
Noted and worked around simulation bugs specific to the Packet Tracer version used.
Repository Structure


How to Use
Download wlc-lap-lab.pkt from this repository.
Open it in Cisco Packet Tracer.
Open the management PC browser and go to the WLC address over HTTPS.
Log in with the admin account you created and review the WLAN and AP Group settings.
Key Takeaways
The DHCP option for the WLC address is what allows lightweight APs to find the controller without manual configuration on each AP.
AP Groups let one controller serve different SSIDs to different areas or departments.
Packet Tracer does not behave exactly like real hardware, so some steps need workarounds.
Reference

This lab was built by following a video tutorial on configuring a WLC and LAPs in Cisco Packet Tracer.

Author

Praveen BSc Hons in Information Technology, University of Vavuniya Cisco CCNA certified (March 2026)
