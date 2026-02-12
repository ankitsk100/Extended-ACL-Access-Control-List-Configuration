# Extended ACL — Allow Only IT PCs to Access DHCP Server

This lab demonstrates an **Extended Access Control List (ACL)** on a Cisco router to enforce a simple policy:

Only **IT department PCs** can access the **DHCP server**  
All other hosts are blocked from reaching the DHCP server  
All hosts can still access other destinations (example: Server1)

> Note: The task statement says “standard ACL”, but the configuration used here is an **extended ACL** (source + destination matching).

---

## Topology

![Topology](docs/topology.png)

### Addressing Summary (from the diagram)
- **IT PCs**
  - PC0: `192.168.1.10/24`
  - PC1: `192.168.1.20/24`
- **HR PCs**
  - PC2: `192.168.1.30/24`
  - PC3: `192.168.1.40/24`
- **Servers (10.10.10.0/24)**
  - DHCP Server: `10.10.10.10/24`
  - Server1: `10.10.10.20/24`
- **Router (R1)**
  - G0/0 (LAN): `192.168.1.1/24`
  - G0/1 (Server LAN): `10.10.10.1/24`

---

## Objective

Create and apply an **extended ACL** so that:
- Only `192.168.1.10` and `192.168.1.20` can reach `10.10.10.10`
- Everyone else is denied to `10.10.10.10`
- All other traffic is permitted (example: access to `10.10.10.20` is allowed)

---

## How It Works (Policy Logic)

The ACL is processed **top to bottom** (first match wins):

1. Permit IT PC0 → DHCP
2. Permit IT PC1 → DHCP
3. Deny ANY → DHCP
4. Permit ANY → ANY (so other traffic still works)

---

## Configuration Files

- Router configs: [`configs/routers/`](configs/routers/)
- Packet Tracer lab: [`packet-tracer/`](packet-tracer/)
- Documentation: [`docs/`](docs/)

---

## Quick Verification (Packet Tracer)

### Test 1 — IT PCs should reach DHCP Server
From **PC0 (192.168.1.10)**:
- Ping `10.10.10.10` → should succeed

From **PC1 (192.168.1.20)**:
- Ping `10.10.10.10` → should succeed

### Test 2 — HR PCs should NOT reach DHCP Server
From **PC2 (192.168.1.30)**:
- Ping `10.10.10.10` → should fail

From **PC3 (192.168.1.40)**:
- Ping `10.10.10.10` → should fail

### Test 3 — Everyone can access other server
From HR PCs (PC2/PC3):
- Ping `10.10.10.20` → should succeed

---

## Useful Show Commands

On R1:
```bash
show access-lists
show ip interface gig0/0
show running-config | section ip access-list
