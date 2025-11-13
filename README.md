# OSPF + HSRP + L3 EtherChannel Redundancy Lab  
A high-availability enterprise campus lab built in GNS3 featuring OSPF routing, HSRP gateway redundancy, and a fully routed EtherChannel core.


## Overview  
This lab simulates a resilient enterprise network using two WAN routers, dual distribution switches, and dual-homed access switches. The environment implements **HSRP first-hop redundancy**, **OSPF Area 0** dynamic routing, and a **Layer-3 EtherChannel core** to provide fast convergence and fault tolerance. Endpoints in separate VLANs validate inter-VLAN routing, redundancy, and upstream connectivity.

This project mirrors real-world campus design principles and includes full documentation, IP addressing plans, failover testing, and verification commands.


## Lab Objectives  
- Deploy HSRP for redundant default gateways across VLANs.  
- Build a routed L3 EtherChannel between distribution switches for loop-free redundancy.  
- Configure OSPF Area 0 across all routed links and ensure proper route propagation.  
- Validate endpoint connectivity, inter-VLAN routing, and WAN availability.  
- Perform structured failover testing and measure MTTR during switch, link, and gateway failures.  
- Apply enterprise-style documentation, change control, and network validation.

## Tools Used  
- **GNS3**
- **VMware Workstation** 
- **Cisco IOSv / IOSv-L2**   
- **Windows Server 2022**  
- **Ubuntu Linux**  

## Network Diagram  
<img width="1045" height="930" alt="image" src="https://github.com/user-attachments/assets/18d0f918-d929-41fc-8744-bb79fb9f30f9" />


## IP Addressing  

### VLANs (SVIs + HSRP)

| VLAN | Subnet | HSRP VIP | DSW1 SVI | DSW2 SVI | Notes |
|------|--------|----------|-----------|-----------|--------|
| 10 | 10.0.10.0/24 | **10.0.10.1** | 10.0.10.2 | 10.0.10.3 | DSW1 Active |
| 20 | 10.0.20.0/24 | **10.0.20.1** | 10.0.20.2 | 10.0.20.3 | DSW2 Active |

### Distribution ↔ Router Links
- R1 ↔ DSW1: **10.0.1.0/30**  
- R2 ↔ DSW2: **10.1.1.0/30**

### Distribution ↔ Distribution Core
- L3 EtherChannel Po1: **10.255.0.0/30**  
  - DSW1 = 10.255.0.1  
  - DSW2 = 10.255.0.2

### Hosts
- Server2022 (VLAN10): `10.0.10.100` → GW `10.0.10.1`  
- Ubuntu (VLAN20): `10.0.20.100` → GW `10.0.20.1`  


## Skills Learned  
- Implementing **HSRP** and understanding Active/Standby gateway behavior  
- Configuring **OSPF Area 0** on Layer-3 switches and routers  
- Building **Layer-3 EtherChannel** to eliminate STP dependency in the core  
- Designing redundant campus topologies with dual-homed access switches  
- Troubleshooting VLAN, STP, trunking, and gateway issues  
- Validating routing, gateway failover, and convergence time  
- Documenting network changes and validation using enterprise standards  


## Lab Setup Summary  
1. Created VLAN 10 and VLAN 20 on all switches.  
2. Configured SVIs for both VLANs on DSW1 and DSW2.  
3. Implemented **HSRP** with split-active design:  
   - DSW1 Active for VLAN10  
   - DSW2 Active for VLAN20  
4. Configured trunks between ASWs and DSWs (allowing VLANs 10 and 20).  
5. Built **L3 EtherChannel Port-Channel1** between DSW1 and DSW2.  
6. Configured routed /30 links from DSWs to WAN routers.  
7. Deployed **OSPF Area 0** on all routed links and SVIs.  
8. Set static default routes on WAN routers and optionally redistributed them.  
9. Assigned static IPs to Server2022 and Ubuntu hosts.  
10. Validated end-to-end connectivity, inter-VLAN routing, and WAN access.


## Failover & Testing Summary  

### HSRP Testing  
- Shutdown VLAN10 SVI on DSW1 → DSW2 becomes Active → gateway changes with minimal packet loss.  
- Preemption restores original Active state once DSW1 returns.

### EtherChannel Testing  
- Removed one EtherChannel member → no traffic loss.  
- Removed entire Port-Channel → OSPF reroutes via WAN routers with short convergence.

### OSPF Testing  
- Verified OSPF neighbors on DSW1↔DSW2 and DSWs↔Routers.  
- Confirmed VLAN routes appear across OSPF domain.  
- Ensured default route was properly propagated or originated.

### Host Validation  
- Server2022 ↔ Ubuntu (inter-VLAN) successful.  
- Both hosts reached upstream ISP paths through correct OSPF routes.  


