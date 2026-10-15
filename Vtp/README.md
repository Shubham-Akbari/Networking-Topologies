# VTP Topology (Cisco Packet Tracer)

A Packet Tracer lab that uses **VTP (VLAN Trunking Protocol)** to distribute VLANs across 7 switches. **Switch0 is the VTP server**, and Switch1 to Switch6 are **VTP clients**. VLANs are created once on the server and learned automatically by all clients. There are 7 VLANs (VLAN 10 to VLAN 16) with 2 PCs each.

## Topology

```
                    Router0 (192.168.0.254)
                          |
 PC0 (v10) ──┐            |
 PC1 (v10) ──┴────── Switch0 [SERVER]
                          |
 PC2 (v11) ──┐            |
 PC3 (v11) ──┴────── Switch1 [client] ──────── Switch2 [client] ──┬── PC4 (v12)
                                                    |            └── PC5 (v12)
                                                    |
                                               Switch3 [client] ──┬── PC6 (v13)
                                                    |            └── PC7 (v13)
                                                    |
 PC8 (v14) ──┐                                      |
 PC9 (v14) ──┴────── Switch4 [client] ──────── Switch5 [client] ──┬── PC10 (v15)
                                                    |            └── PC11 (v15)
                                                    |
                                               Switch6 [client] ──┬── PC12 (v16)
                                                                  └── PC13 (v16)
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Switches | Catalyst 2960-24TT (Switch0–Switch6) | 7 |
| PCs | PC-PT (PC0–PC13) | 14 |

## VTP Roles

| Switch | VTP Mode |
|--------|----------|
| Switch0 | **Server** |
| Switch1 to Switch6 | **Client** |

## VLAN and IP Plan

Subnet mask is not shown in the diagram, so `255.255.255.0` (/24) is assumed.

| VLAN | Subnet | PCs | PC IP Addresses | Switch |
|------|--------|-----|-----------------|--------|
| 10 | 192.168.10.0 | PC0, PC1 | 192.168.10.1, 192.168.10.2 | Switch0 |
| 11 | 192.168.11.0 | PC2, PC3 | 192.168.11.1, 192.168.11.2 | Switch1 |
| 12 | 192.168.12.0 | PC4, PC5 | 192.168.12.1, 192.168.12.2 | Switch2 |
| 13 | 192.168.13.0 | PC6, PC7 | 192.168.13.1, 192.168.13.2 | Switch3 |
| 14 | 192.168.14.0 | PC8, PC9 | 192.168.14.1, 192.168.14.2 | Switch4 |
| 15 | 192.168.15.0 | PC10, PC11 | 192.168.15.1, 192.168.15.2 | Switch5 |
| 16 | 192.168.16.0 | PC12, PC13 | 192.168.16.1, 192.168.16.2 | Switch6 |

Router0 is labeled `192.168.0.254` in the diagram.

## Connections

| Link | Cable (as drawn) | Port type |
|------|------------------|-----------|
| Router0 ↔ Switch0 | Dashed (crossover) | Trunk |
| Switch0 ↔ Switch1 | Dashed (crossover) | Trunk |
| Switch1 ↔ Switch2 | Dashed (crossover) | Trunk |
| Switch2 ↔ Switch3 | Dashed (crossover) | Trunk |
| Switch3 ↔ Switch4 | Dashed (crossover) | Trunk |
| Switch4 ↔ Switch5 | Dashed (crossover) | Trunk |
| Switch5 ↔ Switch6 | Dashed (crossover) | Trunk |
| PCs ↔ their switch | Solid (straight-through) | Access |

## Configuration (example)

The VTP domain name and password are not shown in the diagram. `LAB` and `cisco` below are examples.

### 1. Switch0 (VTP server): create VLANs

```
vtp mode server
vtp domain LAB
vtp password cisco
vtp version 2

vlan 10
vlan 11
vlan 12
vlan 13
vlan 14
vlan 15
vlan 16

interface range fa0/1 - 2
 switchport mode trunk
```

### 2. Switch1 to Switch6 (VTP clients)

```
vtp mode client
vtp domain LAB
vtp password cisco

interface range fa0/1 - 2
 switchport mode trunk
```

Adjust the trunk ports to match the cables in your file.

### 3. Assign PC ports to VLANs

VTP shares the VLAN database only. Each access port must still be set on its own switch, for example Switch1 for VLAN 11:

```
interface range fa0/3 - 4
 switchport mode access
 switchport access vlan 11
```

### 4. Router (only if inter-VLAN routing is needed)

```
interface fa0/0
 no shutdown
interface fa0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.254 255.255.255.0
! ... repeat for VLANs 11 to 16
```

## Verification

- `show vtp status` on every switch: check mode, domain and revision number.
- `show vlan brief` on a client: VLANs 10 to 16 should appear without being created manually.
- `ping` between PCs in the same VLAN, for example PC2 to PC3.

## Notes

- VLANs can only be created or deleted on the **server**. Clients reject manual VLAN changes.
- All switches must use the same VTP domain name and password, and the links between them must be trunks.
- The gateway for each VLAN is not shown in the diagram. The example uses `192.168.x.254` on the router sub-interfaces.
