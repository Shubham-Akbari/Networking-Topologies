# Access Point (Wireless) Topology (Cisco Packet Tracer)

A small Packet Tracer lab that adds **Wi-Fi** to a wired network. Router0 connects to a switch, the switch connects to an **Access Point**, and three wireless clients (a PC, a laptop and a smartphone) join the network through the access point.

## Topology

```
                Router0
                   |
               Switch0
                   |
             Access Point0
            /      |       \
         (Wi-Fi) (Wi-Fi)  (Wi-Fi)
          PC0    Laptop0  Smartphone0
```

## Devices

| Device | Model | Count |
|--------|-------|-------|
| Router | Router-PT (Router0) | 1 |
| Switch | Catalyst 2960-24TT (Switch0) | 1 |
| Access Point | AccessPoint-PT (Access Point0) | 1 |
| PC | PC-PT (PC0) | 1 |
| Laptop | Laptop-PT (Laptop0) | 1 |
| Smartphone | SMARTPHONE-PT (Smartphone0) | 1 |

## Connections

| Link | Type |
|------|------|
| Router0 ↔ Switch0 | Wired, solid line (straight-through) |
| Switch0 ↔ Access Point0 | Wired, solid line (straight-through) |
| Access Point0 ↔ PC0 | Wireless (dotted line) |
| Access Point0 ↔ Laptop0 | Wireless (dotted line) |
| Access Point0 ↔ Smartphone0 | Wireless (dotted line) |

## IP Addressing

The diagram does not show any IP addresses, so the values below are examples **(confirm)**:

| Device | IP Address |
|--------|-----------|
| Router0 | 192.168.1.1 (default gateway) |
| PC0, Laptop0, Smartphone0 | From DHCP, `192.168.1.x` |

Subnet mask: `255.255.255.0` (assumed).

## Configuration (example)

### 1. Router0: interface and DHCP

```
enable
configure terminal
interface fa0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp pool WIFI
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
end
write memory
```

### 2. Access Point0: Wi-Fi settings

Click Access Point0 → **Config** → **Port 1**:

| Field | Value (example) |
|-------|-----------------|
| SSID | MyWiFi |
| Authentication | WPA2-PSK |
| Passphrase | `cisco123` |
| Encryption | AES |

### 3. Wireless clients

- **Laptop0 and Smartphone0** already have a wireless adapter. Go to **Config** → **Wireless0**, set the same SSID, authentication and passphrase, and select **DHCP**.
- **PC0** has a wired network card by default. To make it wireless:
  1. Turn the PC **off**.
  2. In the **Physical** tab, remove the Ethernet module and drag in the **WMP300N** wireless module.
  3. Turn the PC **on** again.
  4. Configure it in **Config** → **Wireless0** like the other clients.

## Testing

- Check that each wireless client gets an IP in `192.168.1.x`.
- `ping 192.168.1.1` from a client to reach the router.
- Ping between PC0, Laptop0 and Smartphone0.
- Change the passphrase on one client and confirm it can no longer connect.

## Notes

- The access point connects at Layer 2. It does not route or hand out IP addresses by itself, so the router (or a DHCP server) must do that.
- Wireless range in Packet Tracer is limited, so keep the clients near the access point.
