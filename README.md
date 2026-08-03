# Enterprise Campus Network with High Availability & Dual ISP Connectivity

A Cisco Packet Tracer project demonstrating a highly available enterprise campus network designed using a three-tier architecture with redundant core, edge, and WAN connectivity. The project implements enterprise networking technologies including HSRP, Rapid PVST+, EtherChannel, eBGP, NAT/PAT, inter-VLAN routing, and DHCP relay while validating network resiliency through failover testing.

<img width="1737" height="719" alt="Screenshot 2026-08-03 at 8 02 31 AM" src="https://github.com/user-attachments/assets/9336bd1d-9d09-4c65-9e2a-34d19d83616c" />


---

# Overview

This project simulates a highly available enterprise campus network following a hierarchical architecture consisting of Access, Core, and Edge layers. The design emphasizes scalability, resiliency, and fault tolerance by incorporating redundant network paths, gateway redundancy, and dual Internet connectivity.

The network provides segmented Data, Voice, Wireless, and Transit VLANs while demonstrating enterprise routing, switching, and WAN technologies commonly deployed in production environments.

---

# Objectives

- Design a scalable three-tier enterprise campus network.
- Implement gateway redundancy using HSRP.
- Configure redundant Layer 2 paths using EtherChannel and Rapid PVST+.
- Provide redundant WAN connectivity using dual ISPs and eBGP.
- Implement inter-VLAN routing using multilayer switches.
- Configure DHCP relay and PAT (NAT Overload).
- Validate network resiliency through failover testing.

---

# Network Architecture

## Core Layer

- Dual multilayer core switches
- Inter-VLAN routing using SVIs
- HSRP gateway redundancy
- Rapid PVST+ primary and secondary root bridge configuration
- LACP EtherChannel between core switches

## Access Layer

- Three Layer 2 access switches
- Redundant EtherChannel uplinks to both core switches
- Data, Voice, and Wireless VLAN segmentation
- End-user, IP phone, and wireless access connectivity

## Edge Layer

- Dual enterprise edge routers
- HSRP edge gateway redundancy
- PAT (NAT Overload)
- Static routing toward internal VLANs
- eBGP peering with upstream ISPs

## WAN

- Two simulated Internet Service Providers
- Separate Autonomous Systems (AS100 and AS200)
- Enterprise Autonomous System (AS300)
- Simulated public loopback networks for Internet reachability testing

---

# Technologies

| Category | Technologies |
|-----------|--------------|
| Routing | Inter-VLAN Routing, Static Routing, eBGP |
| High Availability | HSRP, Rapid PVST+, EtherChannel (LACP) |
| Switching | VLANs, 802.1Q Trunking |
| WAN | Dual ISP Connectivity |
| Network Services | DHCP Relay, NAT/PAT |
| Platform | Cisco Packet Tracer |

---

# Network Implementation

## VLAN Design

| VLAN | Purpose | Default Gateway |
|------|---------|----------------|
| 10 | Data | 192.168.10.254 |
| 20 | Voice | 192.168.20.254 |
| 30 | Wireless | 192.168.30.254 |
| 99 | Core / Edge Transit | 10.99.99.254 |

---

## High Availability Design

Redundancy was implemented throughout the network using multiple enterprise technologies.

- HSRP configured on both the Core and Edge layers
- Active/Standby gateway redundancy
- Rapid PVST+ root bridge optimization
- LACP EtherChannel providing redundant uplinks
- Dual enterprise edge routers
- Dual ISP connectivity

---

## Routing & Internet Connectivity

- Inter-VLAN routing performed by multilayer core switches
- DHCP requests forwarded using `ip helper-address`
- Static default routes configured toward the edge layer
- eBGP configured between enterprise edge routers and simulated ISPs
- PAT (NAT Overload) configured on both edge routers for Internet access

---

# Verification

Network functionality was verified using Cisco IOS commands including:

```text
show standby brief
show spanning-tree
show spanning-tree root
show etherchannel summary
show interfaces trunk
show vlan brief
show ip route
show ip bgp summary
show ip nat translations
ping
traceroute
```

Verification confirmed:

- Successful HSRP operation
- Inter-VLAN communication
- EtherChannel operation
- Internet connectivity through both ISPs
- eBGP neighbor establishment
- Successful NAT translations

---

# Redundancy Validation

To validate the resiliency of the network, failures were simulated at the Access, Core, and Edge layers while monitoring connectivity and failover behavior.

## Access Layer Redundancy

### Test Performed

- Disabled a physical uplink participating in an LACP EtherChannel between an access switch and the core.

### Expected Result

- EtherChannel remains operational using the remaining active member link.
- End-user connectivity is maintained.

### Result

- Traffic continued forwarding through the remaining EtherChannel member.
- Clients maintained connectivity to the default gateway and Internet resources.

**Screenshot**

<img width="1970" height="641" alt="Screenshot 2026-08-03 at 4 17 46 PM" src="https://github.com/user-attachments/assets/78aa88f1-4272-4954-a062-517e38a95b01" />


---

## Core Layer Redundancy

### Test Performed

- Simulated failure of the primary core switch.

### Expected Result

- HSRP transfers gateway ownership to the standby core switch.
- Rapid PVST+ recalculates the spanning-tree topology.
- Inter-VLAN routing continues through the secondary core.

### Result

- HSRP successfully transitioned to the standby core switch.
- Layer 2 convergence completed successfully.
- Inter-VLAN routing and Internet connectivity remained operational.

**Screenshot**

#### CORE 1 Shutdown
<img width="1619" height="664" alt="Screenshot 2026-08-03 at 4 28 41 PM" src="https://github.com/user-attachments/assets/91db926a-2808-43ce-9576-11d97aaa6f9e" />


#### CORE 2 Switch Become Active for all VLANs
```
CORE2#show standby brief 
                     P indicates configured to preempt.
                     |
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Vl10        10   90  P Active   local           unknown         192.168.10.254 
Vl20        20   90  P Active   local           unknown         192.168.20.254 
Vl30        30   90  P Active   local           unknown         192.168.30.254
```

#### CORE 2 Switch Becomes Root Primary For all VLANs
```
CORE2#show spanning-tree 
VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    28682
             Address     0010.113B.2831
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    28682  (priority 28672 sys-id-ext 10)
             Address     0010.113B.2831
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Po15             Desg FWD 12        128.31   P2p
Po14             Desg FWD 12        128.30   P2p
Po16             Desg FWD 12        128.32   P2p

VLAN0020
  Spanning tree enabled protocol rstp
  Root ID    Priority    28692
             Address     0010.113B.2831
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    28692  (priority 28672 sys-id-ext 20)
             Address     0010.113B.2831
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Po15             Desg FWD 12        128.31   P2p
Po14             Desg FWD 12        128.30   P2p
Po16             Desg FWD 12        128.32   P2p

VLAN0030
  Spanning tree enabled protocol rstp
  Root ID    Priority    28702
             Address     0010.113B.2831
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    28702  (priority 28672 sys-id-ext 30)
             Address     0010.113B.2831
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Po15             Desg FWD 12        128.31   P2p
Po14             Desg FWD 12        128.30   P2p
Po16             Desg FWD 12        128.32   P2p

VLAN0099
  Spanning tree enabled protocol rstp
  Root ID    Priority    32867
             Address     0010.113B.2831
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32867  (priority 32768 sys-id-ext 99)
             Address     0010.113B.2831
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Gi1/0/1          Desg FWD 4         128.1    P2p
```
#### PC Ping to ISP2
```
C:\>ping -t isp2

Pinging 9.9.9.9 with 32 bytes of data:

Request timed out.
Reply from 9.9.9.9: bytes=32 time=1ms TTL=253
Reply from 9.9.9.9: bytes=32 time=1ms TTL=253
Reply from 9.9.9.9: bytes=32 time<1ms TTL=253
Reply from 9.9.9.9: bytes=32 time<1ms TTL=253
Reply from 9.9.9.9: bytes=32 time<1ms TTL=253

```
---

## Edge Layer Redundancy

### Test Performed

- Simulated failure of the primary edge router (EDGE1).

### Expected Result

- HSRP transfers the edge virtual gateway to EDGE2.
- Internet traffic exits through the secondary ISP.

### Result

- Gateway failover completed successfully.
- Internal hosts maintained Internet connectivity through EDGE2 and ISP2.
- PAT continued translating outbound traffic using the backup edge router.

**Screenshot**

#### Edge 1 Shutdown
<img width="1763" height="692" alt="Screenshot 2026-08-03 at 4 36 00 PM" src="https://github.com/user-attachments/assets/fb3c6dff-295d-4b95-ae11-20f37ec9e59a" />

#### Edge 2 becomes Active
```
EDGE2#show standby brief 
                     P indicates configured to preempt.
                     |
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Gig0/0/1    1    90  P Active   local           unknown         10.99.99.254
```

#### PC Pinging Edge 2 VIP and ISP2
```
C:\>ping 10.99.99.254

Pinging 10.99.99.254 with 32 bytes of data:

Reply from 10.99.99.254: bytes=32 time<1ms TTL=254
Reply from 10.99.99.254: bytes=32 time<1ms TTL=254
Reply from 10.99.99.254: bytes=32 time<1ms TTL=254
Reply from 10.99.99.254: bytes=32 time<1ms TTL=254

Ping statistics for 10.99.99.254:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping isp2

Pinging 9.9.9.9 with 32 bytes of data:

Reply from 9.9.9.9: bytes=32 time<1ms TTL=253
Reply from 9.9.9.9: bytes=32 time<1ms TTL=253
Reply from 9.9.9.9: bytes=32 time<1ms TTL=253
Reply from 9.9.9.9: bytes=32 time<1ms TTL=253

Ping statistics for 9.9.9.9:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

#### Edge 2 performing NAT to ISP 2
```
EDGE2#show ip nat translations 
Pro  Inside global     Inside local       Outside local      Outside global
icmp 200.200.200.2:44  192.168.10.106:44  9.9.9.9:44         9.9.9.9:44
icmp 200.200.200.2:45  192.168.10.106:45  9.9.9.9:45         9.9.9.9:45
```

---

## Validation Summary

| Component | Failure Simulated | Result |
|-----------|-------------------|--------|
| Access Layer | EtherChannel member failure | Successful failover with uninterrupted connectivity |
| Core Layer | Primary core switch failure | HSRP failover maintained gateway availability |
| Edge Layer | Primary edge router failure | Internet connectivity maintained through secondary edge router |

---

# Simulation Limitations

Cisco Packet Tracer does not support all enterprise networking features. This topology reflects production design practices while acknowledging simulator limitations.

- Two Wireless LAN Controllers are included to represent a high-availability wireless architecture; however, WLC High Availability (HA SSO) is not supported in Packet Tracer.
- Internal BGP (iBGP) between enterprise edge routers was not implemented because Packet Tracer does not support iBGP. External BGP (eBGP) was implemented to demonstrate redundant WAN connectivity.

---

# Key Skills Demonstrated

- Enterprise Network Design
- Routing & Switching
- High Availability
- VLAN Segmentation
- Inter-VLAN Routing
- HSRP
- Rapid PVST+
- LACP EtherChannel
- eBGP
- NAT/PAT
- DHCP Relay
- Cisco IOS CLI
- Network Verification & Troubleshooting

---

# Conclusion

This project demonstrates the design, implementation, and validation of a highly available enterprise campus network using Cisco Packet Tracer. By integrating redundant core and edge infrastructure, dual ISP connectivity, gateway redundancy, Layer 2 resiliency, inter-VLAN routing, and Internet access, the lab provides practical experience with enterprise networking concepts while reinforcing network design, configuration, troubleshooting, and failover validation.
