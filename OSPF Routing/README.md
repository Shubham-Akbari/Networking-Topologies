# Multi-Area OSPF Topology (Cisco Packet Tracer)

A Packet Tracer lab that runs **OSPF across multiple areas**. A small set of routers is joined by **serial point-to-point links**, and each link is labeled with the OSPF area it belongs to (for example `a=0`, `a=10`, `a=20`). Each router also serves a LAN with several switches, VLAN-labeled PCs and some inter-switch redundant links.

> **Note:** The source screenshot is low resolution, so exact IP addresses, router numbers, PC counts and area numbers could not be read reliably. Items marked **(confirm)** should be checked against the `.pkt` file before this README is final.

## Topology Overview

- About **7 routers** (Router-PT), linked by red serial lines, each labeled `a=<area>`.
- Several links are in **area 0 (backbone)**, and others belong to non-backbone areas such as **area 10** and **area 20** **(confirm)**.
- Routers that touch more than one area act as **ABRs (Area Border Routers)**.
- Each router has one or more LANs: switches with 2 to 4 PCs per switch. Some switch pairs are joined by a double cable.
- PCs carry different VLAN/network labels (v2, v3, v4 up to roughly v19) **(confirm)**.

```
                    ┌── Router3 ──(a=10)── Router4 ──(a=20)── Router5
                    │      |  \              |  \                |
 Router2 ──(a=0)────┘   (a=0)  (a=0)      (a=0) (a=0)         (a=0)
      \                  |        \          |     \              |
       (a=0)──────────── Router6 ──(a=10)── Router7 ──(a=20)── Router8
```

This diagram is a simplified view of the layout, not an exact link-by-link map **(confirm)**.

## Addressing Scheme

### WAN (router to router)

Each serial link is its own network, written as `X.0.0.1` on one end and `X.0.0.2` on the other, with `X` from about **10 to 19**:

| Link network | Example ends |
|--------------|--------------|
| 10.0.0.0 | Router2 `10.0.0.1` ↔ Router3 `10.0.0.2` |
| 11.0.0.0 | Router3 `11.0.0.1` ↔ Router4 `11.0.0.2` |
| 12.0.0.0 to 19.0.0.0 | Remaining serial links, one network per link **(confirm)** |

### LAN (router to switch to PCs)

- Each LAN has a gateway on the router, and PCs use addresses in the matching `192.168.x.0`, `200.168.x.0`, `172.x.x.0` and similar networks.
- The default gateway of each PC is its router's LAN interface **(confirm)**.

## Cabling

| Link type | Cable (as drawn) |
|-----------|------------------|
| Router ↔ Router | Red lightning line (serial DCE/DTE) |
| Router ↔ Switch | Solid black |
| Switch ↔ PC | Solid black (straight-through) |
| Switch ↔ Switch | Solid black, or double dashed line for redundant pairs |

## OSPF Configuration (example)

Each router advertises its serial links and its LANs in the area that each link belongs to. Example for an **ABR** with one area 0 link and one area 10 link:

```
interface serial 2/0
 ip address 10.0.0.1 255.0.0.0
 clock rate 64000
 no shutdown

router ospf 1
 router-id 3.3.3.3
 network 10.0.0.0 0.255.255.255 area 0
 network 11.0.0.0 0.255.255.255 area 10
 network 192.168.2.0 0.0.0.255 area 10
```

Rules to follow:

- Every non-backbone area must connect to **area 0** through an ABR.
- Both ends of a link must use the **same area** for that link.
- Use a unique **router-id** on each router.

## Verification

- `show ip ospf neighbor`: neighbors should be in the **FULL** state.
- `show ip route ospf`: routes from other areas appear as `O IA` (inter-area).
- `show ip ospf interface brief`: confirms which area each interface is in.
- `show ip protocols`: lists the OSPF networks and areas.
- `ping` from a PC to its gateway first, then to PCs in other areas.

## Details to Confirm

- Exact area numbers (the diagram shows labels like `a=0`, `a=10`, `a=20`).
- Which routers are ABRs.
- Exact router count and names.
- Exact IP of every LAN gateway, PC and VLAN number.
- Subnet masks (WAN links look classful `/8`; LANs assumed `/24`).
