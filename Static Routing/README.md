# Static Routing Topology (Cisco Packet Tracer)

A large Packet Tracer lab that connects many routers with **serial point-to-point links** and uses **static routes** so every LAN can reach every other LAN. Each router has its own LAN: a gateway interface (`.254`), one or more switches, and 2 PCs.

> **Note:** The source screenshot is low resolution, so exact IP addresses, router numbers and PC counts could not be read reliably. Items marked **(confirm)** should be checked against the `.pkt` file before this README is final.

## Topology Overview

- About **18 routers** (Router-PT), arranged as a mesh of rows and columns linked by red serial lines.
- Each router has a **LAN side**: a gateway address ending in `.254` connected to a 2960-24TT switch with 2 PCs. Some LANs have two switches joined by a dual-cable link (EtherChannel-style, shown with orange/green ports).
- Different LANs use different VLAN labels on the PCs (for example v10, v20, v30, v50, v60, v70, v90, v110, v120, v130, v140, v150, v160, v170, v190) **(confirm)**.

```
 Router0 ── Router1 ── Router2 ── Router3 ── LAN
    |          |          |          |
 Router4 ── Router5 ── Router6 ── Router7 ──────┐
    |          |          |          |          |
  (LANs)     (LANs)     (LANs)     (LAN)     Router8 ── Router9 ── Router10 ── Router11
                                                 |                      |
                                              (LAN)                  Router12 (LAN)
                                                                        |
                                                                     Router13 ── Router14 ... (bottom row)
```

This diagram is a simplified view of the layout, not an exact router-by-router map **(confirm)**.

## Addressing Scheme

### WAN (router to router)

Each serial link is its own small network, written in the diagram as `X.0.0.1` on one end and `X.0.0.2` on the other, where `X` is a number from about **10 to 29**:

| Link network | Example ends |
|--------------|--------------|
| 10.0.0.0 | Router0 `10.0.0.1` ↔ Router1 `10.0.0.2` |
| 11.0.0.0 | Router1 `11.0.0.1` ↔ Router2 `11.0.0.2` |
| 12.0.0.0 to 29.0.0.0 | Remaining serial links, one network per link |

### LAN (router to switch to PCs)

- The router's LAN interface uses an address ending in `.254`, for example `192.168.x.254`, `172.168.x.254`, `172.100.x.254`.
- The two PCs on each LAN use `.1` and `.2` in that same network, and their default gateway is the router's `.254` address **(confirm)**.

## Cabling

| Link type | Cable (as drawn) |
|-----------|------------------|
| Router ↔ Router | Red lightning line (serial DCE/DTE) |
| Router ↔ Switch | Solid black (straight-through or crossover) |
| Switch ↔ PC | Solid black (straight-through) |
| Switch ↔ Switch | Double black line (two parallel cables) |

## Static Routing Configuration (example)

Every router needs a route to every network it is **not** directly connected to. For a router with serial links `10.0.0.0` and `12.0.0.0`:

```
enable
configure terminal

interface serial 2/0
 ip address 10.0.0.1 255.0.0.0
 clock rate 64000
 no shutdown

! Route to a remote LAN through the next-hop router
ip route 192.168.20.0 255.255.255.0 10.0.0.2

! Route to a remote WAN link
ip route 11.0.0.0 255.0.0.0 10.0.0.2
```

Use the format:

```
ip route <destination network> <mask> <next-hop IP>
```

Optionally add a **default route** on edge routers to cut down the number of entries:

```
ip route 0.0.0.0 0.0.0.0 <next-hop IP>
```

## Verification

- `show ip route`: static routes appear with the code `S`.
- `show ip interface brief`: all serial and LAN interfaces should be up/up.
- `ping` from a PC to its own gateway first, then to PCs on far routers.
- `tracert <PC IP>` from a PC to see each router hop.

## Details to Confirm

- Exact router count and names.
- Exact IP of every LAN gateway, PC and VLAN number.
- Serial interface and clock-rate side (DCE) for each link.
- Subnet masks (WAN links look like classful `/8`; LANs assumed `/24`).
