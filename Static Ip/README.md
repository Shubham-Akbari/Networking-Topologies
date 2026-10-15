# Static IP Topology (Cisco Packet Tracer)

A single-subnet Packet Tracer lab where all hosts and the router use **statically assigned IPs** in the `123.1.0.0` network. Switches are connected in a tree layout, with the router attached to Switch0.

## Topology

```
                         Router0 (123.1.0.254)
                               |
 PC0 (.1) ──┐                  |
 PC1 (.2) ──┴── Switch0 ──── Switch1 ──── Switch2 ──┬── PC3 (.4)
                  |            |  |          |      └── PC4 (.5)
                  |          PC2 (.3)        |
               Switch3         |          Switch5 ──┬── PC5 (.6)
              /      \      Switch4                 └── PC6 (.7)
       PC10 (.11)  PC9 (.10)  /   \
                         PC8 (.9) PC7 (.8)
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Switches | Catalyst 2960-24TT (Switch0–Switch5) | 6 |
| PCs | PC-PT (PC0–PC10) | 11 |

## IP Addressing

Network: `123.1.0.0` (subnet mask is not shown in the diagram, so `255.255.255.0` / `/24` is assumed)

| Device | IP Address | Connected To |
|--------|-----------|--------------|
| Router0 | 123.1.0.254 | Switch0 |
| PC0 | 123.1.0.1 | Switch0 |
| PC1 | 123.1.0.2 | Switch0 |
| PC2 | 123.1.0.3 | Switch1 |
| PC3 | 123.1.0.4 | Switch2 |
| PC4 | 123.1.0.5 | Switch2 |
| PC5 | 123.1.0.6 | Switch5 |
| PC6 | 123.1.0.7 | Switch5 |
| PC7 | 123.1.0.8 | Switch4 |
| PC8 | 123.1.0.9 | Switch4 |
| PC9 | 123.1.0.10 | Switch3 |
| PC10 | 123.1.0.11 | Switch3 |

> Default gateway for all PCs: `123.1.0.254` (Router0).

## Connections

| Link | Cable (as drawn) |
|------|------------------|
| Router0 ↔ Switch0 | Dashed (crossover) |
| Switch0 ↔ Switch1 | Dashed (crossover) |
| Switch1 ↔ Switch2 | Dashed (crossover) |
| Switch0 ↔ Switch3 | Dashed (crossover) |
| Switch1 ↔ Switch4 | Dashed (crossover) |
| Switch2 ↔ Switch5 | Dashed (crossover) |
| PCs ↔ their switch | Solid (straight-through) |

## How to Configure a PC

1. Click the PC → **Desktop** → **IP Configuration**
2. Select **Static**
3. Enter the IP address from the table above
4. Subnet mask: `255.255.255.0`
5. Default gateway: `123.1.0.254`

## Testing

- From any PC, run `ping 123.1.0.254` to check the router.
- Ping between PCs on different switches, e.g. `ping 123.1.0.11` from PC0, to verify the whole switch chain.
- Use **Simulation mode** to watch packets travel across the switches.

## Notes

- The label next to PC7 in the diagram reads `123.1.0.0.8`, which looks like a typo. It is documented here as `123.1.0.8`.
- All devices share one broadcast domain (no VLANs shown), so routing between subnets is not required.
