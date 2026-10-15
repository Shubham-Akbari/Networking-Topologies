# EtherChannel Topology (Cisco Packet Tracer)

A Packet Tracer lab that uses **EtherChannel (link aggregation)** between 7 switches. Each switch-to-switch connection is a bundle of parallel links (shown as 3 cables in the diagram) combined into one logical link. There are 8 VLANs (VLAN 10 to VLAN 17) with 2 PCs each.

## Topology

```
                     Router0 (192.168.0.254)
                           |
 PC0 (v10) ──┐             |
 PC1 (v11) ──┴────────  Switch0
                           ‖  EtherChannel 1
 PC2 (v12) ──┐             ‖
 PC3 (v12) ──┴────────  Switch1
                           ‖  EtherChannel 2
                        Switch2 ──┬── PC4 (v13)
                           ‖      └── PC5 (v13)
                           ‖  EtherChannel 3
 PC6 (v14) ──┐             ‖
 PC7 (v14) ──┴────────  Switch3
                           ‖  EtherChannel 4
 PC8 (v15) ──┐             ‖
 PC9 (v15) ──┴────────  Switch4
                           ‖  EtherChannel 5
 PC10 (v16) ─┐             ‖
 PC11 (v16) ─┴────────  Switch5
                           ‖  EtherChannel 6
                        Switch6 ──┬── PC12 (v17)
                                  └── PC13 (v17)
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Switches | Catalyst 2960-24TT (Switch0–Switch6) | 7 |
| PCs | PC-PT (PC0–PC13) | 14 |

## EtherChannel Links

| Channel | Between | Links in bundle (as drawn) |
|---------|---------|----------------------------|
| EtherChannel 1 | Switch0 ↔ Switch1 | 3 |
| EtherChannel 2 | Switch1 ↔ Switch2 | 3 |
| EtherChannel 3 | Switch2 ↔ Switch3 | 3 |
| EtherChannel 4 | Switch3 ↔ Switch4 | 3 |
| EtherChannel 5 | Switch4 ↔ Switch5 | 3 |
| EtherChannel 6 | Switch5 ↔ Switch6 | 3 |

Router0 ↔ Switch0 is a single link (dashed, crossover).

## VLAN and IP Plan

Subnet mask is not shown in the diagram, so `255.255.255.0` (/24) is assumed.

| VLAN | Subnet | PC | PC IP Address | Switch |
|------|--------|----|---------------|--------|
| 10 | 192.168.10.0 | PC0 | 192.168.10.1 | Switch0 |
| 11 | 192.168.11.0 | PC1 | 192.168.11.1 | Switch0 |
| 12 | 192.168.12.0 | PC2, PC3 | 192.168.12.1, 192.168.12.2 | Switch1 |
| 13 | 192.168.13.0 | PC4, PC5 | 192.168.13.1, 192.168.13.2 | Switch2 |
| 14 | 192.168.14.0 | PC6, PC7 | 192.168.14.1, 192.168.14.2 | Switch3 |
| 15 | 192.168.15.0 | PC8, PC9 | 192.168.15.1, 192.168.15.2 | Switch4 |
| 16 | 192.168.16.0 | PC10, PC11 | 192.168.16.1, 192.168.16.2 | Switch5 |
| 17 | 192.168.17.0 | PC12, PC13 | 192.168.17.1, 192.168.17.2 | Switch6 |

Router0 is labeled `192.168.0.254` in the diagram.

## Configuration (example)

The EtherChannel protocol (PAgP, LACP or static) and the exact ports are not visible in the diagram. The example below uses **LACP** and ports `Fa0/1 - 3`; adjust to match your cables.

### 1. Bundle the links (on both ends of each channel)

Switch0 ↔ Switch1 (EtherChannel 1), on Switch0:

```
interface range fa0/2 - 4
 channel-group 1 mode active
 channel-protocol lacp
exit
interface port-channel 1
 switchport mode trunk
```

Repeat on the other switch with the same channel number and a matching mode (`active` with `active` or `passive`). Use channel-groups 2 to 6 for the other bundles.

### 2. Create the VLANs

Create VLANs 10 to 17 on every switch (or use VTP), then put each PC port in its VLAN:

```
interface fa0/5
 switchport mode access
 switchport access vlan 12
```

### 3. Router (only if inter-VLAN routing is needed)

```
interface fa0/0
 no shutdown
interface fa0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.254 255.255.255.0
! ... repeat for VLANs 11 to 17
```

## Verification

- `show etherchannel summary`: the port-channel should show **SU** (in use, Layer 2) and member ports **P** (bundled).
- `show interfaces trunk`: the port-channels should appear as trunks.
- `ping` between PCs in the same VLAN, for example PC2 to PC3.
- Shut down one cable of a bundle and confirm traffic keeps flowing.

## Notes

- All links in a bundle must have the same speed, duplex and VLAN/trunk settings.
- Both ends of a channel must use compatible modes (LACP `active`/`passive`, or both `on`).
- Switch0 has PC0 and PC1 in two different VLANs (10 and 11).
- Bundle sizes (3 links) are read from the drawing and may be 2 or 3 in your file.
