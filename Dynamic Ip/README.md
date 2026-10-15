# Dynamic IP (DHCP) Topology (Cisco Packet Tracer)

A single-network Packet Tracer lab where PCs get their IP addresses **dynamically (DHCP)**. Router0 serves the `172.16.0.0` network, and the switches are connected in a chain below it.

## Topology

```
                Router0 (172.16.0.0 network)
                      |
 PC0 ──┐              |
 PC1 ──┴──────── Switch0
                      |
                   Switch1 ──────── Switch2 ──────── Switch3
                   /     \          /     \          /     \
                PC2     PC3      PC4     PC5      PC6     PC7
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Switches | Catalyst 2960-24TT (Switch0–Switch3) | 4 |
| PCs | PC-PT (PC0–PC7) | 8 |

## Connections

| Link | Cable (as drawn) |
|------|------------------|
| Router0 ↔ Switch0 | Dashed (crossover) |
| Switch0 ↔ Switch1 | Dashed (crossover) |
| Switch1 ↔ Switch2 | Dashed (crossover) |
| Switch2 ↔ Switch3 | Dashed (crossover) |
| PC0, PC1 ↔ Switch0 | Solid (straight-through) |
| PC2, PC3 ↔ Switch1 | Solid (straight-through) |
| PC4, PC5 ↔ Switch2 | Solid (straight-through) |
| PC6, PC7 ↔ Switch3 | Solid (straight-through) |

## Addressing

| Item | Value |
|------|-------|
| Network | `172.16.0.0` |
| Assignment | DHCP (PCs set to **DHCP**, not static) |
| DHCP server | Router0 |

The diagram only shows the network address. Router interface IP, subnet mask and DHCP pool range are **not visible**, so the values below are examples to adjust.

## Router DHCP Configuration (example)

```
enable
configure terminal
interface fa0/0
 ip address 172.16.0.1 255.255.0.0
 no shutdown
exit
ip dhcp excluded-address 172.16.0.1 172.16.0.10
ip dhcp pool LAN
 network 172.16.0.0 255.255.0.0
 default-router 172.16.0.1
 dns-server 8.8.8.8
end
write memory
```

## How to Set a PC to DHCP

1. Click the PC → **Desktop** → **IP Configuration**
2. Select **DHCP**
3. Wait for the address to be assigned (it should be in `172.16.x.x`)

## Testing

- Check each PC's IP in **IP Configuration** (or run `ipconfig` in Command Prompt).
- `ping 172.16.0.1` from any PC to reach the router.
- Ping between PCs on different switches, e.g. PC0 to PC7, to verify the whole chain.
- On the router, run `show ip dhcp binding` to see which PC got which address.

## Notes

- All devices share one broadcast domain (no VLANs shown).
- Router0's DHCP pool and interface IP must be configured for the PCs to receive addresses.
