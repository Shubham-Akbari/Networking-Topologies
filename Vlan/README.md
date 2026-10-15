# VLAN Topology (Cisco Packet Tracer)

A Cisco Packet Tracer lab with **7 VLANs (10–70)**, each on its own access switch, connected in a daisy chain and uplinked to a single router.

- **File:** `vlan topology.pkt`
- **Tool:** Cisco Packet Tracer 9.0

## Topology

```
Router0 (Fa0/0)
   |  trunk
Switch0 ── Switch1 ── Switch2 ── Switch3 ── Switch4 ── Switch5 ── Switch6
(VLAN 10)  (VLAN 20)  (VLAN 30)  (VLAN 40)  (VLAN 50)  (VLAN 60)  (VLAN 70)
 2 PCs      2 PCs      2 PCs      2 PCs      2 PCs      2 PCs      2 PCs
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Switches | Catalyst 2960-24TT (Switch0–Switch6) | 7 |
| PCs | PC-PT (PC0–PC13) | 14 |

## VLAN Plan

| Switch | VLAN | Access Ports | Hosts |
|--------|------|--------------|-------|
| Switch0 | 10 | Fa0/3, Fa0/4 | 2 PCs |
| Switch1 | 20 | Fa0/3, Fa0/4 | 2 PCs |
| Switch2 | 30 | Fa0/3, Fa0/4 | 2 PCs |
| Switch3 | 40 | Fa0/3, Fa0/4 | 2 PCs |
| Switch4 | 50 | Fa0/3, Fa0/4 | 2 PCs |
| Switch5 | 60 | Fa0/3, Fa0/4 | 2 PCs |
| Switch6 | 70 | Fa0/3, Fa0/4 | 2 PCs |

## Cabling

| From | To | Cable |
|------|----|-------|
| Router0 Fa0/0 | Switch0 Fa0/1 | Crossover |
| Switch*N* Fa0/2 | Switch*N+1* Fa0/1 | Crossover (inter-switch chain) |
| Switch*N* Fa0/3, Fa0/4 | PC FastEthernet0 | Straight-through |

## Current Configuration State

What is saved in the `.pkt` file right now:

- Switch0 `Fa0/1` (to the router) is set to **trunk**.
- Each switch has its own VLAN created and its two PC ports set to `access` in that VLAN.
- Router0 `Fa0/0` has **no IP address and no sub-interfaces**.
- PCs have **no static IP configuration** saved.
- Inter-switch ports (`Fa0/1`, `Fa0/2`) are left at default (not explicitly trunked).
- Switch6 has one PC on `Fa0/2` instead of `Fa0/4`, so that port stays in VLAN 1.

## Suggested Next Steps

1. Set the inter-switch links to trunks and allow VLANs 10–70:
   ```
   interface range fa0/1 - 2
    switchport mode trunk
    switchport trunk allowed vlan 10,20,30,40,50,60,70
   ```
   (create all VLANs on every switch, or use VTP)
2. Configure router-on-a-stick on Router0:
   ```
   interface fa0/0
    no shutdown
   interface fa0/0.10
    encapsulation dot1Q 10
    ip address 192.168.10.1 255.255.255.0
   ! repeat for VLANs 20–70
   ```
3. Assign IPs and default gateways to the PCs.
4. Test with `ping` within a VLAN and across VLANs.

## How to Open

Open `vlan topology.pkt` with Cisco Packet Tracer (version 8.x or later recommended).
