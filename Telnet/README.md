# Telnet Remote Access Topology (Cisco Packet Tracer)

A Packet Tracer lab with **two routers connected by a serial link**, each serving its own LAN. The goal is to **remotely log in to a router using Telnet** from a PC on the other side. The diagram shows the login credentials for this lab: **username `123`, password `abc`**.

## Topology

```
 Router0 (192.168.1.254) ═══ serial ═══ Router1 (193.168.1.254)
    |                                       |
 Switch0                                 Switch1 ──────── Switch2
  /    \                                  /    \           /    \
PC0    PC1                             PC2    PC3       PC4    PC5

 LAN A: 192.168.1.0/24                  LAN B: 193.168.1.0/24
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Routers | Router-PT (Router0, Router1) | 2 |
| Switches | Catalyst 2960-24TT (Switch0–Switch2) | 3 |
| PCs | PC-PT (PC0–PC5) | 6 |

## Network Plan

Subnet masks are not shown in the diagram, so `255.255.255.0` (/24) is assumed for the LANs. The serial link and PC addresses are not shown either, so those values are examples **(confirm)**.

| Network | Subnet | Gateway (Router) | PCs |
|---------|--------|------------------|-----|
| LAN A (Switch0) | 192.168.1.0/24 | Router0: `192.168.1.254` | PC0 `192.168.1.1`, PC1 `192.168.1.2` |
| LAN B (Switch1, Switch2) | 193.168.1.0/24 | Router1: `193.168.1.254` | PC2 `193.168.1.1`, PC3 `193.168.1.2`, PC4 `193.168.1.3`, PC5 `193.168.1.4` |
| Serial link | 10.0.0.0/30 | Router0 `10.0.0.1`, Router1 `10.0.0.2` | |

## Connections

| Link | Cable (as drawn) |
|------|------------------|
| Router0 ↔ Router1 | Red lightning line (serial DCE/DTE) |
| Router0 ↔ Switch0 | Solid (straight-through) |
| Router1 ↔ Switch1 | Solid (straight-through) |
| Switch1 ↔ Switch2 | Solid (crossover) |
| PCs ↔ their switch | Solid (straight-through) |

## Configuration (example)

### 1. Router0

```
enable
configure terminal
hostname Router0

interface fa0/0
 ip address 192.168.1.254 255.255.255.0
 no shutdown
interface serial 2/0
 ip address 10.0.0.1 255.255.255.252
 clock rate 64000
 no shutdown
exit

! Route to the other LAN
ip route 193.168.1.0 255.255.255.0 10.0.0.2

! Telnet access
username 123 password abc
enable secret abc
line vty 0 4
 login local
 transport input telnet
end
write memory
```

### 2. Router1

```
enable
configure terminal
hostname Router1

interface fa0/0
 ip address 193.168.1.254 255.255.255.0
 no shutdown
interface serial 2/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown
exit

ip route 192.168.1.0 255.255.255.0 10.0.0.1

username 123 password abc
enable secret abc
line vty 0 4
 login local
 transport input telnet
end
write memory
```

Interface names (`fa0/0`, `serial 2/0`) depend on the modules in your routers. Check them with `show ip interface brief`.

### 3. PCs

Click each PC → **Desktop** → **IP Configuration** and set a static IP from the table above, with the router's `.254` address as the default gateway.

## How to Telnet

1. On PC0 (or any PC), open **Desktop** → **Command Prompt**.
2. Run: `telnet 193.168.1.254` (to log in to Router1), or from a PC on LAN B: `telnet 192.168.1.254`.
3. Enter username `123` and password `abc`.
4. Type `enable` and the enable secret to get privileged mode.

## Testing

- `ping 193.168.1.254` from PC0 to confirm the routers can reach each other.
- `telnet` to the remote router and run `show ip interface brief`.
- Try a wrong username or password and confirm that login fails.

## Security Notes

- Telnet sends everything, including passwords, in **plain text**. It is fine for a lab, but real networks should use **SSH** instead.
- The credentials `123` / `abc` are weak and only suitable for practice.
- The `enable secret` value in the examples (`abc`) is an assumption, so change it as needed.
