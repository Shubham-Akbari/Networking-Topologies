# Web, DNS and DHCP Server Topology (Cisco Packet Tracer)

A Packet Tracer lab with a central **server (Server0)** that provides **Web (HTTP), DNS and DHCP** services to PCs spread across five departments: **marketing, accounts, other, sales and admin**. Five switches connect the departments, and Router0 is the default gateway.

## Topology

```
                         Router0 (192.168.1.254)
                               |
 PC0 ──┐                       |                         ┌── PC9
 PC1 ──┴── Switch3 (other)     |      Switch2 (admin) ───┴── PC10
              ¦                |           ¦
              ¦                |           ¦
 PC2 ──┐      ¦                |           ¦        ┌── PC7
 PC3 ──┴── Switch4 ┄┄┄┄┄┄ Switch0 ┄┄┄┄┄┄ Switch1 ────┼── PC8
         (accounts)    (marketing)        (sales)    └── Server0 (192.168.1.1)
                         /  |  \                          DNS / Web / DHCP
                      PC4  PC5  PC6
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Server | Server-PT (Server0) | 1 |
| Switches | Catalyst 2960-24TT (Switch0–Switch4) | 5 |
| PCs | PC-PT (PC0–PC10) | 11 |

## Departments

| Department | Switch | PCs |
|------------|--------|-----|
| marketing | Switch0 | PC4, PC5, PC6 |
| accounts | Switch4 | PC2, PC3 |
| other | Switch3 | PC0, PC1 |
| sales | Switch1 | PC7, PC8 |
| admin | Switch2 | PC9, PC10 |

The diagram labels the groups by department name. The VLAN numbers are not shown, so the departments are assumed to share the `192.168.1.0/24` network unless you configured VLANs **(confirm)**.

## IP Addressing

| Device | IP Address | Notes |
|--------|-----------|-------|
| Router0 | 192.168.1.254 | Default gateway |
| Server0 | 192.168.1.1 | Static IP, DNS server |
| PCs | From DHCP | Assigned by Server0 |

Subnet mask is not shown, so `255.255.255.0` (/24) is assumed.

## Connections

| Link | Cable (as drawn) |
|------|------------------|
| Router0 ↔ Switch0 | Solid (straight-through) |
| Switch0 ↔ Switch4 | Dashed (crossover) |
| Switch0 ↔ Switch1 | Dashed (crossover) |
| Switch4 ↔ Switch3 | Dashed (crossover) |
| Switch1 ↔ Switch2 | Dashed (crossover) |
| Server0 ↔ Switch1 | Solid (straight-through) |
| PCs ↔ their switch | Solid (straight-through) |

## Configuration

### 1. Server0: static IP

Click Server0 → **Desktop** → **IP Configuration**:

- IP address: `192.168.1.1`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.1.254`
- DNS server: `192.168.1.1`

### 2. Server0: DHCP service

Click Server0 → **Services** → **DHCP** → turn **On** and set the pool (values below are examples):

| Field | Value |
|-------|-------|
| Pool name | serverPool |
| Default gateway | 192.168.1.254 |
| DNS server | 192.168.1.1 |
| Start IP address | 192.168.1.100 |
| Subnet mask | 255.255.255.0 |
| Maximum users | 100 |

### 3. Server0: DNS service

Click Server0 → **Services** → **DNS** → turn **On**, then add an **A record**:

| Name | Address |
|------|---------|
| www.example.com | 192.168.1.1 |

Use whatever domain name your lab requires **(confirm)**.

### 4. Server0: Web service

Click Server0 → **Services** → **HTTP** → turn **On**. Edit `index.html` if you want a custom page.

### 5. Router0

```
interface fa0/0
 ip address 192.168.1.254 255.255.255.0
 no shutdown
```

### 6. PCs

Click each PC → **Desktop** → **IP Configuration** → select **DHCP**. The PC should receive an address, gateway and DNS server automatically.

> If the departments are on separate VLANs with separate subnets, the router must route between them and each LAN interface needs `ip helper-address 192.168.1.1` so DHCP requests reach the server.

## Testing

- Check that every PC gets an IP in `192.168.1.x` through DHCP.
- `ping 192.168.1.1` from a PC to reach the server.
- `nslookup www.example.com` from a PC should return `192.168.1.1`.
- Open the **Web Browser** on a PC and go to `http://www.example.com` (or `http://192.168.1.1`).

## Notes

- Several switch ports show orange in the screenshot. This is normal while spanning tree converges. Wait a moment before testing.
- The server's own IP must be static, and its address must be outside the DHCP pool range.
