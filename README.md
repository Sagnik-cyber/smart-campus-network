# Smart Campus Network Design Using LAN, VLAN and Network Topologies

A scalable campus network designed and simulated in **Cisco Packet Tracer**, using a hierarchical extended-star topology, department-wise VLANs, inter-VLAN routing on a Layer 3 core switch, and a central server.

> **Course:** Computer Networks
> **Author:** YOUR NAME (Roll No. XXXXX)
> **Institution:** YOUR COLLEGE NAME
> **Tool:** Cisco Packet Tracer (version X.X)

---

## Table of Contents
1. [Objective](#objective)
2. [Project Targets](#project-targets)
3. [Network Topology](#network-topology)
4. [Devices Used](#devices-used)
5. [VLAN and IP Addressing Plan](#vlan-and-ip-addressing-plan)
6. [Cabling and Port Map](#cabling-and-port-map)
7. [Configuration Summary](#configuration-summary)
8. [How to Run This Project](#how-to-run-this-project)
9. [Testing and Results](#testing-and-results)
10. [Effect of VLANs on Broadcast Domains](#effect-of-vlans-on-broadcast-domains)
11. [OSI and TCP/IP Layers in This Network](#osi-and-tcpip-layers-in-this-network)
12. [Advantages of VLAN Segmentation](#advantages-of-vlan-segmentation)
13. [Repository Structure](#repository-structure)
14. [Conclusion](#conclusion)

---

## Objective

To design and simulate a scalable campus network using an appropriate network topology, LAN technologies, VLANs, switches, routers and transmission links, and to evaluate the advantages of logical network segmentation.

## Project Targets

| # | Target | Status |
|---|--------|--------|
| 1 | Identify suitable topologies for the campus | Done |
| 2 | Design the entire network using Cisco Packet Tracer | Done |
| 3 | Create individual VLANs for each department | Done |
| 4 | Observe the effect of VLAN segmentation on the broadcast domain | Done |
| 5 | Explain the OSI and TCP/IP layers in the proposed network | Done |

---

## Network Topology

**Chosen topology: Hierarchical extended star (core to access).**

![Network Topology](screenshots/topology.png)

**Why this topology?**
- **Scalable:** a new department only needs a new access switch and one new uplink to the core.
- **Fault isolation:** if one access switch or cable fails, the rest of the campus keeps working.
- **Easy management:** a central core makes troubleshooting and policy control simple.
- **Layered design:** core, distribution (merged into the Layer 3 core here) and access layers each have a clear role.

**Other topologies considered:**

| Topology | Reason not chosen as the main design |
|----------|--------------------------------------|
| Bus | Single point of failure, poor performance, obsolete |
| Ring | One break can disrupt the whole network |
| Full mesh | Very expensive for a campus (too many links) |
| Star | Good, but a single central device does not scale to many buildings, so it is extended into a hierarchy |

---

## Devices Used

| Device | Model | Hostname | Quantity |
|--------|-------|----------|----------|
| Router | Cisco 2911 | Edge-R1 | 1 |
| Layer 3 core switch | Cisco 3650-24PS | Core-SW | 1 |
| Access switches | Cisco 2960-24TT | SW-CSE, SW-ECE, SW-ADMIN, SW-LIB | 4 |
| Server | Server-PT | Server0 (DNS + HTTP) | 1 |
| PCs | PC-PT | 2 per department | 8 |

---

## VLAN and IP Addressing Plan

| Department | VLAN ID | VLAN Name | Subnet | Gateway (SVI on Core-SW) |
|------------|---------|-----------|--------|--------------------------|
| CSE | 10 | CSE | 192.168.10.0/24 | 192.168.10.1 |
| ECE | 20 | ECE | 192.168.20.0/24 | 192.168.20.1 |
| Admin | 30 | ADMIN | 192.168.30.0/24 | 192.168.30.1 |
| Library | 40 | LIBRARY | 192.168.40.0/24 | 192.168.40.1 |
| Servers | 50 | SERVERS | 192.168.50.0/24 | 192.168.50.1 |

**Router to core link:** 10.0.0.0/30 (Edge-R1 = 10.0.0.1, Core-SW = 10.0.0.2)

**Host addresses**

| Device | IP Address | Gateway |
|--------|-----------|---------|
| PC-CSE1 / PC-CSE2 | 192.168.10.11 / .12 | 192.168.10.1 |
| PC-ECE1 / PC-ECE2 | 192.168.20.11 / .12 | 192.168.20.1 |
| PC-ADMIN1 / PC-ADMIN2 | 192.168.30.11 / .12 | 192.168.30.1 |
| PC-LIB1 / PC-LIB2 | 192.168.40.11 / .12 | 192.168.40.1 |
| Server0 | 192.168.50.10 | 192.168.50.1 |

---

## Cabling and Port Map

| From | To | Link Type |
|------|----|-----------|
| Edge-R1 Gig0/0 | Core-SW Gig1/0/1 | Routed link |
| Core-SW Gig1/0/2 | SW-CSE Gig0/1 | 802.1Q trunk |
| Core-SW Gig1/0/3 | SW-ECE Gig0/1 | 802.1Q trunk |
| Core-SW Gig1/0/4 | SW-ADMIN Gig0/1 | 802.1Q trunk |
| Core-SW Gig1/0/5 | SW-LIB Gig0/1 | 802.1Q trunk |
| Core-SW Gig1/0/6 | Server0 Fa0 | Access port (VLAN 50) |
| PCs | Access switch Fa0/1, Fa0/2 | Access ports |

Copper straight-through cables are used for all links.

---

## Configuration Summary

Full running configurations are in the [`configs/`](configs/) folder.

**Access switch (example: SW-CSE)**
```
vlan 10
 name CSE
interface range fastEthernet 0/1 - 10
 switchport mode access
 switchport access vlan 10
interface gigabitEthernet 0/1
 switchport mode trunk
```

**Core Layer 3 switch (inter-VLAN routing)**
```
ip routing
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown
! ...repeated for VLANs 20, 30, 40, 50
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

**Edge router**
```
interface gigabitEthernet 0/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown
ip route 192.168.0.0 255.255.0.0 10.0.0.2
```

---

## How to Run This Project

1. Install **Cisco Packet Tracer** (free from Cisco Networking Academy).
2. Download or clone this repository:
   ```bash
   git clone https://github.com/YOUR-USERNAME/smart-campus-network.git
   ```
3. Open `campus_network.pkt` in Packet Tracer (File > Open).
4. Open any PC's **Desktop > Command Prompt** and run the ping tests below.
5. Switch to **Simulation Mode** to view packet flow and the OSI layers.

---

## Testing and Results

| Test | From | To | Result |
|------|------|----|--------|
| Same VLAN | PC-CSE1 | PC-CSE2 | Success |
| Default gateway | PC-CSE1 | 192.168.10.1 | Success |
| Inter-VLAN | PC-CSE1 | PC-ECE1 | Success |
| To server | PC-LIB1 | 192.168.50.10 | Success |
| To router | PC-ADMIN1 | 10.0.0.1 | Success |
| Web (HTTP) | Any PC | http://192.168.50.10 | Page loads |
| DNS | Any PC | http://campus.local | Page loads |

Screenshots are in the [`screenshots/`](screenshots/) folder.

Verification commands used: `show vlan brief`, `show interfaces trunk`, `show ip interface brief`, `show ip route`.

---

## Effect of VLANs on Broadcast Domains

| Stage | Broadcast domains | Behaviour |
|-------|-------------------|-----------|
| Before VLANs | **1** | An ARP broadcast from one PC floods all 8 PCs |
| After VLANs | **5** | A broadcast stays inside its own VLAN only |

**Before VLANs**

![Before VLAN](screenshots/before_vlan_broadcast.png)

**After VLANs**

![After VLAN](screenshots/after_vlan_broadcast.png)

**Observation:** after segmentation, an ARP broadcast from PC-CSE1 reaches only devices in VLAN 10. ECE, Admin and Library do not receive it, which reduces unnecessary traffic and improves performance.

---

## OSI and TCP/IP Layers in This Network

| OSI Layer | TCP/IP Layer | Where it appears in this project |
|-----------|--------------|----------------------------------|
| 7 Application | Application | HTTP (web server), DNS (name resolution) |
| 6 Presentation | Application | Data formatting (HTML text) |
| 5 Session | Application | Session between the browser and the server |
| 4 Transport | Transport | TCP (HTTP) and UDP (DNS) with port numbers |
| 3 Network | Internet | IP addressing, SVIs, inter-VLAN routing, router |
| 2 Data Link | Network Access | Ethernet frames, MAC addresses, 802.1Q VLAN tags, switches |
| 1 Physical | Network Access | Copper cables, NICs, electrical signals |

![OSI Layers](screenshots/osi_layers.png)

---

## Advantages of VLAN Segmentation

- **Smaller broadcast domains:** less broadcast traffic and better performance.
- **Security:** departments are logically isolated; traffic between them passes through the Layer 3 core where policies can be applied.
- **Flexibility:** users can be moved between VLANs by configuration, not re-cabling.
- **Easier management:** problems are contained within one VLAN.
- **Cost-effective:** one physical switch can serve several logical networks.

---

## Repository Structure

```
smart-campus-network/
├── campus_network.pkt        # Packet Tracer project file
├── README.md                 # This file
├── report.pdf                # Full project report
├── configs/                  # Running configs of each device
│   ├── Edge-R1.txt
│   ├── Core-SW.txt
│   ├── SW-CSE.txt
│   ├── SW-ECE.txt
│   ├── SW-ADMIN.txt
│   └── SW-LIB.txt
└── screenshots/              # Topology, tests, broadcast and OSI screenshots
```

---

## Conclusion

The hierarchical extended-star topology with VLAN segmentation gives a campus network that is scalable, secure and easy to manage. Moving from one flat network to five VLANs reduced the broadcast domain from all hosts to a single department, while inter-VLAN routing on the Layer 3 core kept full connectivity between departments and the server.

**Future improvements:** redundant core switches with EtherChannel, DHCP per VLAN, ACLs between departments, wireless access points, and STP/HSRP for high availability.

---

*Made for the Computer Networks course project.*
