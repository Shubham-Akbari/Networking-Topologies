# Cisco Packet Tracer Networking Labs

A collection of hands-on **Cisco Packet Tracer** labs covering core networking topics: IP addressing, VLANs, VTP, EtherChannel, static and dynamic routing, OSPF, network services, wireless and remote access. Each lab lives in its own folder with the Packet Tracer file (`.pkt`), a topology screenshot and a `README` explaining the setup.

## Labs

| # | Folder | Topic | What it covers |
|---|--------|-------|----------------|
| 1 | [Static Ip](./Static%20Ip) | Static IP addressing | Manual IPs on PCs and the router in a single subnet |
| 2 | [Dynamic Ip](./Dynamic%20Ip) | Dynamic IP (DHCP) | PCs get addresses automatically from the router |
| 3 | [Vlan](./Vlan) | VLANs | Separate VLANs per switch, access and trunk ports, router uplink |
| 4 | [DHCP in Diffrent Vlan](./DHCP%20in%20Diffrent%20Vlan) | DHCP with VLANs | One DHCP server serving multiple VLANs (router-on-a-stick) |
| 5 | [Vtp](./Vtp) | VTP | VTP server and clients distributing VLANs across switches |
| 6 | [Etherchannel](./Etherchannel) | EtherChannel | Bundling parallel links between switches into one logical link |
| 7 | [Static Routing](./Static%20Routing) | Static routing | Manual routes across many routers joined by serial links |
| 8 | [Dynamic Routing](./Dynamic%20Routing) | Dynamic routing | Routers learning routes automatically (RIP / OSPF / EIGRP) |
| 9 | [OSPF Routing](./OSPF%20Routing) | Multi-area OSPF | OSPF areas, backbone area 0 and area border routers |
| 10 | [WEB,DNS,DHCP Server](./WEB%2CDNS%2CDHCP%20Server) | Network services | One server providing Web, DNS and DHCP to several departments |
| 11 | [access point topology](./access%20point%20topology) | Wireless | Wi-Fi clients connecting through an access point |
| 12 | [Telnet](./Telnet) | Remote access | Telnet login to a remote router across a serial link |

## Suggested Learning Order

1. **Basics:** Static Ip, then Dynamic Ip
2. **Switching:** Vlan, DHCP in Diffrent Vlan, Vtp, Etherchannel
3. **Routing:** Static Routing, Dynamic Routing, OSPF Routing
4. **Services and access:** WEB,DNS,DHCP Server, access point topology, Telnet

## How to Use

1. Install **Cisco Packet Tracer** (the labs were saved with version 9.x).
2. Clone or download this repository:
   ```
   git clone https://github.com/Shubham-Akbari/<repository-name>.git
   ```
3. Open a lab folder and double-click the `.pkt` file, or use **File → Open** in Packet Tracer.
4. Read that folder's `README` for the topology, IP plan and configuration steps.
5. Use the verification commands in each README (`show ip route`, `show vlan brief`, `ping`, and so on) to test the lab.

## Repository Structure

```
.
├── DHCP in Diffrent Vlan/
├── Dynamic Ip/
├── Dynamic Routing/
├── Etherchannel/
├── OSPF Routing/
├── Static Ip/
├── Static Routing/
├── Telnet/
├── Vlan/
├── Vtp/
├── WEB,DNS,DHCP Server/
├── access point topology/
└── README.md
```

Each lab folder contains:

- `*.pkt`: the Packet Tracer file
- topology screenshot (`.png`)
- `README.md`: lab description, IP plan and configuration

## Topics Covered

- IP addressing (static and DHCP)
- VLANs, trunking and inter-VLAN routing
- VTP (server and client modes)
- EtherChannel (link aggregation)
- Static routing
- Dynamic routing protocols and OSPF areas
- Network services (Web, DNS, DHCP)
- Wireless access points
- Remote access with Telnet

## Tools

- Cisco Packet Tracer
- Cisco IOS CLI

## Author

**Shubham Akbari**
GitHub: [@Shubham-Akbari](https://github.com/Shubham-Akbari)
