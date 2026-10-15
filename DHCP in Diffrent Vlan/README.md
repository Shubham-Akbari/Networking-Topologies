# DHCP with Multiple VLANs Topology (Cisco Packet Tracer)

A Packet Tracer lab with **10 VLANs (VLAN 2 to VLAN 11)**, each with its own `192.168.x.0` subnet. Each PC sits in a different VLAN and gets its IP through **DHCP**. Router0 does the inter-VLAN routing (router-on-a-stick) and acts as the DHCP server.

## Topology

```
                     Router0 (192.168.0.254)
                           |
 PC2 (v2)  ──┐             |
 PC3 (v3)  ──┴────────  Switch0
                           |
 PC1 (v4)  ──┐             |
 PC0 (v5)  ──┴────────  Switch1 ──────── Switch2 ──┬── PC4 (v6)
                                            |      └── PC5 (v7)
                                            |
                          Switch4 ──────── Switch3 ──┬── PC7 (v8)
                          /     \                    └── PC6 (v9)
                  PC9 (v10)   PC8 (v11)
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Switches | Catalyst 2960-24TT (Switch0–Switch4) | 5 |
| PCs | PC-PT (PC0–PC9) | 10 |

## VLAN and Subnet Plan

| VLAN | Subnet (assumed /24) | Address shown in diagram | PC | Switch |
|------|----------------------|--------------------------|----|--------|
| 2 | 192.168.2.0 | 192.168.2.1 | PC2 | Switch0 |
| 3 | 192.168.3.0 | 192.168.3.1 | PC3 | Switch0 |
| 4 | 192.168.4.0 | 192.168.4.1 | PC1 | Switch1 |
| 5 | 192.168.5.0 | 192.168.5.1 | PC0 | Switch1 |
| 6 | 192.168.6.0 | 192.168.6.1 | PC4 | Switch2 |
| 7 | 192.168.7.0 | 192.168.7.1 | PC5 | Switch2 |
| 8 | 192.168.8.0 | 192.168.8.1 | PC7 | Switch3 |
| 9 | 192.168.9.0 | 192.168.9.1 | PC6 | Switch3 |
| 10 | 192.168.10.0 | 192.168.10.1 | PC9 | Switch4 |
| 11 | 192.168.11.0 | 192.168.11.1 | PC8 | Switch4 |

Router0 is labeled `192.168.0.254` in the diagram.

> The `.1` addresses in the diagram are assumed to be each VLAN's **default gateway** (router sub-interface). The PCs themselves get other addresses from DHCP (for example `192.168.2.2`). If these labels are meant to be the PC addresses, adjust the pools below.

## Connections

| Link | Cable (as drawn) |
|------|------------------|
| Router0 ↔ Switch0 | Dashed (crossover), **trunk** |
| Switch0 ↔ Switch1 | Dashed (crossover), **trunk** |
| Switch1 ↔ Switch2 | Dashed (crossover), **trunk** |
| Switch2 ↔ Switch3 | Dashed (crossover), **trunk** |
| Switch3 ↔ Switch4 | Dashed (crossover), **trunk** |
| PCs ↔ their switch | Solid (straight-through), **access** port |

## Configuration (example)

### 1. Switches: create VLANs and trunk ports

```
vlan 2
vlan 3
! ... repeat up to vlan 11

interface range fa0/1 - 2
 switchport mode trunk
```

Put each PC port in its VLAN, for example PC2 on Switch0:

```
interface fa0/3
 switchport mode access
 switchport access vlan 2
```

### 2. Router: sub-interfaces (router-on-a-stick) and DHCP

```
interface fa0/0
 no shutdown
interface fa0/0.2
 encapsulation dot1Q 2
 ip address 192.168.2.1 255.255.255.0
interface fa0/0.3
 encapsulation dot1Q 3
 ip address 192.168.3.1 255.255.255.0
! ... repeat for VLANs 4 to 11

ip dhcp pool VLAN2
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
ip dhcp pool VLAN3
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
! ... repeat for VLANs 4 to 11
```

### 3. PCs

Click the PC → **Desktop** → **IP Configuration** → select **DHCP**.

## Testing

- Check that each PC gets an address from its own VLAN's subnet.
- `ping` the PC's gateway (for example `192.168.2.1`).
- `ping` a PC in a different VLAN to verify inter-VLAN routing.
- On the router, run `show ip dhcp binding`.

## Notes

- Subnet masks are not shown in the diagram, so `255.255.255.0` (/24) is assumed.
- Several switch ports show orange (not forwarding yet) in the screenshot. This is normal right after startup while spanning tree converges. Wait a moment before testing.
