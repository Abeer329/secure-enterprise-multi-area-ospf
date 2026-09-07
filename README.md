# Secure Enterprise Network – Multi-Area OSPF Lab

## Overview
This GNS3 lab demonstrates a small enterprise network using Cisco IOS routers with VLAN segmentation, DHCP, ACLs, SSH management, multi-area OSPF, an Area Border Router (ABR), inter-area routing, and OSPF route summarization.

## Topology
- **R1**: HQ router and Router-on-a-Stick gateway for enterprise VLANs
- **R2**: Area Border Router (ABR) connecting OSPF Area 0 and Area 1
- **R3**: Branch router in Area 1
- **Area 0 link**: `10.0.0.0/30` between R1 and R2
- **Area 1 link**: `10.0.0.4/30` between R2 and R3
- **R3 branch LAN**: `192.168.3.0/24`

## HQ VLAN Addressing
| VLAN / Function | Network | Gateway |
|---|---|---|
| Sales | 192.168.0.0/25 | 192.168.0.1 |
| HR | 192.168.0.128/26 | 192.168.0.129 |
| IT | 192.168.0.192/27 | 192.168.0.193 |
| Servers | 192.168.0.224/27 | 192.168.0.225 |
| Management | 192.168.1.0/28 | 192.168.1.1 |

## OSPF Design
- R1 uses OSPF router ID `1.1.1.1`
- R2 uses OSPF router ID `2.2.2.2`
- R3 uses OSPF router ID `3.3.3.3`
- R1 ↔ R2 operates in **Area 0**
- R2 ↔ R3 operates in **Area 1**
- R2 is the **ABR**
- `passive-interface default` is used, and only router-to-router links are enabled for OSPF Hellos.

## Inter-Area Routing
R3 learns Area 0 routes as `O IA` routes. This verifies that R2 is correctly exchanging inter-area routing information between Area 0 and Area 1.

## OSPF Summarization
R2 summarizes the HQ `192.168.0.x` VLSM networks with:

```cisco
router ospf 1
 area 0 range 192.168.0.0 255.255.255.0
```

R3 therefore receives:

```text
O IA 192.168.0.0/24
```

instead of four individual HQ routes.

## Security and Services
R1 also demonstrates:
- DHCP pools for enterprise VLANs
- Router-on-a-Stick using 802.1Q subinterfaces
- Extended ACL filtering between Sales and HR
- SSH v2 remote management
- VTY access restricted to the IT subnet
- Passive OSPF interfaces on user-facing networks

## Troubleshooting Performed
The lab included practical troubleshooting of:
- OSPF adjacency failure caused by `passive-interface default`
- OSPF area mismatch between R2 and R3
- Layer-2 switchport vs routed-port behavior
- Serial interface DCE/clocking behavior
- Inter-area route verification and metric comparison

## Verification Commands
```cisco
show ip ospf neighbor
show ip route ospf
show ip ospf
show ip ospf interface
show running-config
```

## Key Learning Outcomes
- Built and verified a multi-area OSPF design
- Configured and validated an ABR
- Distinguished `O` intra-area routes from `O IA` inter-area routes
- Implemented inter-area route summarization
- Used passive interfaces to reduce unnecessary OSPF Hellos
- Troubleshot adjacency and area configuration issues
- Combined routing, segmentation, DHCP, ACLs, and secure management in one enterprise lab

## Repository Structure
```text
.
├── README.md
├── configs/
│   ├── R1-running-config-sanitized.txt
│   ├── R2-running-config-clean.txt
│   └── R3-running-config-clean.txt
└── images/
    ├── topology.png
    ├── r2-ospf-neighbors.png
    └── r3-ospf-routes.png
```

> Credentials and password hashes are intentionally removed from the public configuration files.
