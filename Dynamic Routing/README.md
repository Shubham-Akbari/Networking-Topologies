# Dynamic Routing Topology (Cisco Packet Tracer)

A large Packet Tracer lab where routers are connected by **serial point-to-point links** and learn each other's networks automatically using a **dynamic routing protocol** (RIP, OSPF or EIGRP). Each router has its own LAN with a switch and 2 PCs, so no static routes are needed once the protocol is running.

> **Note:** The source screenshot is low resolution, so exact IP addresses, router numbers, PC counts and the routing protocol could not be read reliably. Items marked **(confirm)** should be checked against the `.pkt` file before this README is final.

## Topology Overview

- About **16 routers** (Router-PT) laid out as a mesh, joined by red serial lines.
- Each router has a **LAN side**: a gateway interface (address ending in `.254`) connected to a 2960-24TT switch with 2 PCs. A few LANs have two switches joined by a dual-cable link.
- PCs carry different VLAN/network labels per LAN (for example v5, v6, v7, v8, v9, v10, v11, v12, v13, v14, v15, v16, v17, v18, v19, v20) **(confirm)**.
- Compared with the static routing lab, the layout is almost the same. The difference is how routes are learned.

```
 Router0 ── Router1 ── Router2 ── Router3 ── LAN
               |          |          |
            Router4 ── Router5 ── Router6 ── Router7 ──── LAN
               |          |          |          |
             (LAN)      (LANs)      (LAN)    Router8 ── Router9 ── Router10 ── Router11
                                                |                      |
                                              (LAN)                Router12 ... Router13
                                                                       |
                                                              Router15 ── Router14
```

This diagram is a simplified view of the layout, not an exact router-by-router map **(confirm)**.

## Addressing Scheme

### WAN (router to router)

Each serial link is its own network, written as `X.0.0.1` on one end and `X.0.0.2` on the other, where `X` runs from about **10 to 25**:

| Link network | Example ends |
|--------------|--------------|
| 10.0.0.0 | Router0 `10.0.0.1` ↔ Router1 `10.0.0.2` |
| 11.0.0.0 | Router1 `11.0.0.1` ↔ Router2 `11.0.0.2` |
| 12.0.0.0 to 25.0.0.0 | Remaining serial links, one network per link |

### LAN (router to switch to PCs)

- The router's LAN interface uses an address ending in `.254`, for example `192.168.x.254`, `172.168.x.254`, `110.x.x.254`.
- The two PCs on each LAN use `.1` and `.2` in that network, with the router's `.254` address as default gateway **(confirm)**.

## Cabling

| Link type | Cable (as drawn) |
|-----------|------------------|
| Router ↔ Router | Red lightning line (serial DCE/DTE) |
| Router ↔ Switch | Solid black |
| Switch ↔ PC | Solid black (straight-through) |
| Switch ↔ Switch | Double black line (two parallel cables) |

## Dynamic Routing Configuration (examples)

The protocol is not visible in the diagram. Pick the one used in your file **(confirm)**.

### Option A: RIP v2

```
router rip
 version 2
 no auto-summary
 network 10.0.0.0
 network 12.0.0.0
 network 192.168.5.0
```

### Option B: OSPF

```
router ospf 1
 router-id 1.1.1.1
 network 10.0.0.0 0.255.255.255 area 0
 network 12.0.0.0 0.255.255.255 area 0
 network 192.168.5.0 0.0.0.255 area 0
```

### Option C: EIGRP

```
router eigrp 100
 no auto-summary
 network 10.0.0.0
 network 12.0.0.0
 network 192.168.5.0
```

On every router, advertise **each directly connected network** (its serial links and its LAN). Set the serial interfaces first:

```
interface serial 2/0
 ip address 10.0.0.1 255.0.0.0
 clock rate 64000
 no shutdown
```

## Verification

- `show ip route`: learned routes appear with codes `R` (RIP), `O` (OSPF) or `D` (EIGRP).
- `show ip protocols`: confirms the protocol and advertised networks.
- `show ip interface brief`: all serial and LAN interfaces should be up/up.
- `ping` from a PC to its gateway first, then to PCs on far routers.
- Shut one serial link and check that traffic finds another path (the mesh gives redundancy).

## Details to Confirm

- Routing protocol in use (RIP, OSPF or EIGRP) and its settings.
- Exact router count and names.
- Exact IP of every LAN gateway, PC and VLAN number.
- Subnet masks (WAN links look classful `/8`; LANs assumed `/24`).
