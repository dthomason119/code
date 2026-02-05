# Cisco Nexus 9K BGP EVPN VXLAN Configuration
## Network Design Documentation

### Overview
This repository contains the BGP EVPN VXLAN configuration for a Cisco Nexus 93180YC-FX2 spine-leaf fabric with the following topology:
- **2 Spine Switches** (SPINE-01, SPINE-02)
- **4 Leaf Switches** (LEAF-01, LEAF-02, LEAF-03, LEAF-04)
- **vPC Multi-Homing**: LEAF-01/02 as vPC pair 1, LEAF-03/04 as vPC pair 2

---

## Network Topology

```
                  ┌─────────────┐        ┌─────────────┐
                  │  SPINE-01   │        │  SPINE-02   │
                  │ 10.255.255.1│        │10.255.255.2 │
                  └──────┬──────┘        └──────┬──────┘
                         │                      │
        ┌────────────────┼──────────────────────┼────────────────┐
        │                │                      │                │
        │                │                      │                │
   ┌────▼────┐      ┌────▼────┐          ┌────▼────┐      ┌────▼────┐
   │ LEAF-01 │◄─────►│ LEAF-02 │          │ LEAF-03 │◄─────►│ LEAF-04 │
   │  .11    │ vPC-1 │  .12    │          │  .13    │ vPC-2 │  .14    │
   └─────────┘       └─────────┘          └─────────┘       └─────────┘
   Anycast VTEP:     10.255.255.100       Anycast VTEP:     10.255.255.200
```

---

## IP Addressing Scheme

### Underlay Network (Point-to-Point Links)

#### Spine-01 to Leafs
| Link | Spine-01 Interface | Spine-01 IP | Leaf Interface | Leaf IP | Subnet |
|------|-------------------|-------------|----------------|---------|---------|
| To LEAF-01 | Ethernet1/1 | 10.0.1.0/31 | Ethernet1/1 | 10.0.1.1/31 | /31 |
| To LEAF-02 | Ethernet1/2 | 10.0.1.2/31 | Ethernet1/1 | 10.0.1.3/31 | /31 |
| To LEAF-03 | Ethernet1/3 | 10.0.1.4/31 | Ethernet1/1 | 10.0.1.5/31 | /31 |
| To LEAF-04 | Ethernet1/4 | 10.0.1.6/31 | Ethernet1/1 | 10.0.1.7/31 | /31 |

#### Spine-02 to Leafs
| Link | Spine-02 Interface | Spine-02 IP | Leaf Interface | Leaf IP | Subnet |
|------|-------------------|-------------|----------------|---------|---------|
| To LEAF-01 | Ethernet1/1 | 10.0.2.0/31 | Ethernet1/2 | 10.0.2.1/31 | /31 |
| To LEAF-02 | Ethernet1/2 | 10.0.2.2/31 | Ethernet1/2 | 10.0.2.3/31 | /31 |
| To LEAF-03 | Ethernet1/3 | 10.0.2.4/31 | Ethernet1/2 | 10.0.2.5/31 | /31 |
| To LEAF-04 | Ethernet1/4 | 10.0.2.6/31 | Ethernet1/2 | 10.0.2.7/31 | /31 |

### Loopback Addresses

| Device | Loopback0 (Router-ID) | Loopback1 (VTEP) | Loopback1 Secondary |
|--------|----------------------|------------------|---------------------|
| SPINE-01 | 10.255.255.1/32 | N/A | N/A |
| SPINE-02 | 10.255.255.2/32 | N/A | N/A |
| LEAF-01 | 10.255.255.11/32 | 10.255.255.100/32 | 10.255.255.111/32 |
| LEAF-02 | 10.255.255.12/32 | 10.255.255.100/32 | 10.255.255.112/32 |
| LEAF-03 | 10.255.255.13/32 | 10.255.255.200/32 | 10.255.255.113/32 |
| LEAF-04 | 10.255.255.14/32 | 10.255.255.200/32 | 10.255.255.114/32 |

### Management Network (vPC Keepalive)
| Device | Management IP | Purpose |
|--------|--------------|---------|
| LEAF-01 | 192.168.100.1/24 | vPC Keepalive |
| LEAF-02 | 192.168.100.2/24 | vPC Keepalive |
| LEAF-03 | 192.168.100.3/24 | vPC Keepalive |
| LEAF-04 | 192.168.100.4/24 | vPC Keepalive |

---

## VLAN and VNI Mapping

### Layer 2 VNI (VLAN to VNI Mapping)

| VLAN ID | VLAN Name | VNI | Subnet | Gateway | Description |
|---------|-----------|-----|--------|---------|-------------|
| 10 | TENANT_A_WEB | 10010 | 10.10.10.0/24 | 10.10.10.1 | Tenant A Web Tier |
| 11 | TENANT_A_APP | 10011 | 10.10.11.0/24 | 10.10.11.1 | Tenant A App Tier |
| 12 | TENANT_A_DB | 10012 | 10.10.12.0/24 | 10.10.12.1 | Tenant A Database Tier |
| 13 | TENANT_B_WEB | 10013 | 10.10.13.0/24 | 10.10.13.1 | Tenant B Web Tier |
| 14 | TENANT_B_APP | 10014 | 10.10.14.0/24 | 10.10.14.1 | Tenant B App Tier |
| 15 | TENANT_B_DB | 10015 | 10.10.15.0/24 | 10.10.15.1 | Tenant B Database Tier |
| 16 | TENANT_C_WEB | 10016 | 10.10.16.0/24 | 10.10.16.1 | Tenant C Web Tier |
| 17 | TENANT_C_APP | 10017 | 10.10.17.0/24 | 10.10.17.1 | Tenant C App Tier |
| 18 | TENANT_C_DB | 10018 | 10.10.18.0/24 | 10.10.18.1 | Tenant C Database Tier |
| 19 | TENANT_D_WEB | 10019 | 10.10.19.0/24 | 10.10.19.1 | Tenant D Web Tier |
| 20 | TENANT_D_APP | 10020 | 10.10.20.0/24 | 10.10.20.1 | Tenant D App Tier |

**Subnet Range Used**: 10.10.10.0/24 - 10.10.20.0/24 (from Class A 10.0.0.0/16)

---

## Multi-Tenant VRF Configuration

### Layer 3 VNI (VRF to VNI Mapping)

| VRF Name | L3 VNI | Route Distinguisher | Route Target | VLANs Included |
|----------|--------|---------------------|--------------|----------------|
| TENANT_A | 50001 | auto | auto evpn | 10, 11, 12 |
| TENANT_B | 50002 | auto | auto evpn | 13, 14, 15 |
| TENANT_C | 50003 | auto | auto evpn | 16, 17, 18 |
| TENANT_D | 50004 | auto | auto evpn | 19, 20 |

### Tenant Descriptions
- **TENANT_A**: Web, Application, and Database tiers for Tenant A
- **TENANT_B**: Web, Application, and Database tiers for Tenant B
- **TENANT_C**: Web, Application, and Database tiers for Tenant C
- **TENANT_D**: Web and Application tiers for Tenant D

---

## BGP Configuration

### AS Number
- **AS Number**: 65000 (iBGP)

### BGP Peering
- **Control Plane**: BGP EVPN Address Family
- **Underlay**: OSPF (Area 0.0.0.0)
- **Overlay**: BGP L2VPN EVPN

#### Spine Configuration
- Spines act as **BGP Route Reflectors**
- All leaf switches peer with both spines for redundancy

#### Leaf Configuration
- Leafs are **BGP Route Reflector Clients**
- Each leaf peers with both spines using loopback0 as update-source

---

## Route Distinguisher (RD) and Route Target (RT)

### Configuration Strategy
- **RD**: Auto-generated (format: <router-id>:<vni>)
- **RT**: Auto-generated for both import and export
- **RT Format**: AS:VNI (e.g., 65000:10010)

### Per-VNI Configuration

#### Layer 2 VNI RD/RT (Auto-generated examples)
| VNI | RD Example | RT Import | RT Export |
|-----|-----------|-----------|-----------|
| 10010 | 10.255.255.11:10010 | 65000:10010 | 65000:10010 |
| 10011 | 10.255.255.11:10011 | 65000:10011 | 65000:10011 |
| 10012 | 10.255.255.11:10012 | 65000:10012 | 65000:10012 |
| ... | ... | ... | ... |

#### Layer 3 VNI RD/RT (Auto-generated examples)
| VRF | L3 VNI | RD Example | RT Import | RT Export |
|-----|--------|-----------|-----------|-----------|
| TENANT_A | 50001 | 10.255.255.11:50001 | 65000:50001 | 65000:50001 |
| TENANT_B | 50002 | 10.255.255.11:50002 | 65000:50002 | 65000:50002 |
| TENANT_C | 50003 | 10.255.255.11:50003 | 65000:50003 | 65000:50003 |
| TENANT_D | 50004 | 10.255.255.11:50004 | 65000:50004 | 65000:50004 |

---

## vPC Configuration

### vPC Pair 1: LEAF-01 and LEAF-02
- **vPC Domain**: 1
- **vPC Peer-Link**: Port-channel 100 (Ethernet1/47-48)
- **vPC VLAN**: 3000
- **Anycast VTEP**: 10.255.255.100/32
- **Keepalive**: 192.168.100.1 ↔ 192.168.100.2

### vPC Pair 2: LEAF-03 and LEAF-04
- **vPC Domain**: 2
- **vPC Peer-Link**: Port-channel 200 (Ethernet1/47-48)
- **vPC VLAN**: 3000
- **Anycast VTEP**: 10.255.255.200/32
- **Keepalive**: 192.168.100.3 ↔ 192.168.100.4

### vPC Features Enabled
- **peer-gateway**: Enables peer gateway feature for active-active forwarding
- **layer3 peer-router**: Optimizes routing for vPC
- **ip arp synchronize**: Synchronizes ARP entries between vPC peers

---

## VXLAN Configuration

### NVE Interface
- **Interface**: nve1
- **Source Interface**: loopback1 (VTEP address)
- **Host Reachability**: BGP EVPN

### Multicast Groups (BUM Traffic)
| VNI | Multicast Group |
|-----|----------------|
| 10010 | 239.1.1.10 |
| 10011 | 239.1.1.11 |
| 10012 | 239.1.1.12 |
| 10013 | 239.1.1.13 |
| 10014 | 239.1.1.14 |
| 10015 | 239.1.1.15 |
| 10016 | 239.1.1.16 |
| 10017 | 239.1.1.17 |
| 10018 | 239.1.1.18 |
| 10019 | 239.1.1.19 |
| 10020 | 239.1.1.20 |

---

## Anycast Gateway Configuration

### Fabric Anycast Gateway MAC
- **MAC Address**: 0000.2222.3333
- **Configuration**: `fabric forwarding anycast-gateway-mac 0000.2222.3333`
- **Purpose**: Provides consistent default gateway across all leaf switches

### Gateway Addresses (Same across all leafs)
All SVIs use the same gateway IP address across the fabric for seamless mobility:
- VLAN 10: 10.10.10.1/24
- VLAN 11: 10.10.11.1/24
- VLAN 12: 10.10.12.1/24
- ... and so on

---

## Best Practices Implemented

### Based on Cisco Nexus 9K VXLAN BGP EVPN Deployment Guide

1. **Underlay Network**
   - OSPF for underlay routing (simple, fast convergence)
   - Point-to-point interfaces with /31 subnets
   - MTU 9216 (Jumbo frames) for VXLAN encapsulation overhead

2. **Overlay Network**
   - BGP EVPN for control plane
   - Spines as Route Reflectors (scalability)
   - Auto-generated RD/RT (operational simplicity)

3. **vPC Design**
   - Anycast VTEP IP for vPC pairs
   - Separate management network for vPC keepalive
   - Layer 3 peer-router for optimal routing

4. **Multi-Tenancy**
   - VRF for tenant isolation
   - Separate L3 VNI per tenant
   - Auto route-target for simplified configuration

5. **High Availability**
   - Dual spine design (no single point of failure)
   - vPC for host multi-homing
   - Redundant BGP peering (each leaf to both spines)

---

## Configuration Files

- **spine-01.txt**: Configuration for Spine Switch 1
- **spine-02.txt**: Configuration for Spine Switch 2
- **leaf-01.txt**: Configuration for Leaf Switch 1 (vPC Pair 1)
- **leaf-02.txt**: Configuration for Leaf Switch 2 (vPC Pair 1)
- **leaf-03.txt**: Configuration for Leaf Switch 3 (vPC Pair 2)
- **leaf-04.txt**: Configuration for Leaf Switch 4 (vPC Pair 2)

---

## Deployment Steps

### Pre-requisites
1. Ensure all switches are running compatible NX-OS version (9.2(x) or later)
2. Physical cabling matches the topology diagram
3. Management network connectivity established

### Configuration Deployment Order

1. **Configure Spines First**
   - Apply spine-01.txt to SPINE-01
   - Apply spine-02.txt to SPINE-02
   - Verify underlay connectivity

2. **Configure vPC Peer 1**
   - Apply leaf-01.txt to LEAF-01
   - Apply leaf-02.txt to LEAF-02
   - Verify vPC peer-link is up
   - Verify vPC domain status

3. **Configure vPC Peer 2**
   - Apply leaf-03.txt to LEAF-03
   - Apply leaf-04.txt to LEAF-04
   - Verify vPC peer-link is up
   - Verify vPC domain status

### Verification Commands

#### Underlay Verification
```
show ip ospf neighbors
show ip route ospf
show ip interface brief
```

#### Overlay Verification
```
show bgp l2vpn evpn summary
show nve peers
show vxlan
```

#### vPC Verification
```
show vpc
show vpc brief
show vpc consistency-parameters global
```

#### EVPN Verification
```
show bgp l2vpn evpn
show l2route evpn mac all
show l2route evpn mac-ip all
show fabric forwarding anycast-gateway-mac
```

---

## Troubleshooting

### Common Issues and Resolution

1. **BGP Neighbors Not Establishing**
   - Verify loopback0 reachability via underlay
   - Check OSPF adjacencies
   - Verify BGP configuration (AS number, neighbor IPs)

2. **VXLAN Tunnels Not Forming**
   - Verify NVE interface is up
   - Check loopback1 reachability
   - Verify BGP EVPN routes are being received

3. **vPC Issues**
   - Verify vPC peer-link is operational
   - Check vPC keepalive connectivity
   - Review vPC consistency parameters

4. **No Connectivity Between Hosts**
   - Verify host VLANs are configured
   - Check VLAN-to-VNI mapping
   - Verify SVI configuration and status
   - Check anycast gateway MAC configuration

---

## Support and References

### Cisco Documentation
- [Cisco Nexus 9000 Series NX-OS VXLAN Configuration Guide](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/92x/vxlan-92x/configuration/guide/b-cisco-nexus-9000-series-nx-os-vxlan-configuration-guide-92x.html)
- [BGP EVPN Configuration](https://www.cisco.com/c/en/us/support/switches/nexus-9000-series-switches/products-installation-and-configuration-guides-list.html)
- [vPC Configuration Guide](https://www.cisco.com/c/en/us/support/switches/nexus-9000-series-switches/products-installation-and-configuration-guides-list.html)

### Hardware Specifications
- **Switch Model**: Cisco Nexus 93180YC-FX2
- **Form Factor**: 1RU Fixed Switch
- **Ports**: 48x 1/10/25G SFP28 + 6x 40/100G QSFP28

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-02-05 | Initial | Initial BGP EVPN VXLAN configuration |

---

## License
This configuration is provided as-is for reference and educational purposes.
