# Day 8 Lab: IPv4 Addressing and Basic Router Configuration

## Objective
Configure a router (R1) with IPv4 addresses on all interfaces, connect three separate LANs through three switches, and verify end-to-end connectivity between hosts on each network.

## Topology
![Network Topology](01-networking/lab-photos/day-08-ipv4-addresses.png)

The topology consists of one router (R1) connecting three LANs, each on its own subnet:

| Network | Switch | Host | Router Interface |
|---|---|---|---|
| 15.0.0.0/8 | SW1 (2960-24TT) | PC1 | G0/0 |
| 182.98.0.0/16 | SW2 (2960-24TT) | PC2 | G0/1 |
| 201.191.20.0/24 | SW3 (2960-24TT) | PC3 | G0/2 |

## IP Addressing Table

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| R1 | G0/0 | 15.0.0.1 | 255.0.0.0 |
| R1 | G0/1 | 182.98.0.1 | 255.255.0.0 |
| R1 | G0/2 | 201.191.20.1 | 255.255.255.0 |
| PC1 | NIC | 15.0.0.x | 255.0.0.0 |
| PC2 | NIC | 182.98.0.x | 255.255.0.0 |
| PC3 | NIC | 201.191.20.x | 255.255.255.0 |

> Default gateway for each PC is R1's interface on the matching subnet.

## Steps Performed

1. **Configure R1's hostname**
   Set the router's hostname to identify it clearly in the topology (e.g., `R1`).

2. **View R1's interfaces**
   Used a `show` command (`show ip interface brief`) to list R1's interfaces, their assigned IP addresses, and their up/down status before making any changes.

3. **Configure IP addresses and enable interfaces**
   Assigned the appropriate IPv4 address and subnet mask to each of R1's three interfaces (G0/0, G0/1, G0/2), enabled each interface with `no shutdown`, and added descriptive interface descriptions for documentation purposes.

4. **Verify interfaces again**
   Ran `show ip interface brief` a second time to confirm all three interfaces were up/up with the correct IP addresses.

5. **Review and save the configuration**
   Viewed the running configuration (`show running-config`) to confirm the changes were applied correctly, then saved the configuration to NVRAM (`copy running-config startup-config`).

6. **Configure the PCs**
   Assigned static IP addresses, subnet masks, and default gateways to PC1, PC2, and PC3 in Packet Tracer, matching each host to its LAN's subnet.

7. **Test connectivity**
   Used `ping` from PC1 to PC2 and from PC1 to PC3 to confirm end-to-end connectivity across all three subnets through R1.

## Key Commands Used

```
enable
configure terminal
hostname R1

interface g0/0
 description Link to SW1 - PC1 LAN
 ip address 15.0.0.1 255.0.0.0
 no shutdown

interface g0/1
 description Link to SW2 - PC2 LAN
 ip address 182.98.0.1 255.255.0.0
 no shutdown

interface g0/2
 description Link to SW3 - PC3 LAN
 ip address 201.191.20.1 255.255.255.0
 no shutdown

exit
show ip interface brief
show running-config
copy running-config startup-config
```

## Verification

- `show ip interface brief` confirmed all three R1 interfaces were **up/up** with the correct IP addresses.
- Pings from PC1 to PC2 and PC1 to PC3 were **successful**, confirming R1 was correctly routing between the three connected subnets.

## Skills Demonstrated

- Basic router configuration (hostname, interfaces, descriptions)
- IPv4 addressing and subnetting across multiple LANs
- Interface verification using `show` commands
- Saving and validating router configuration
- End-to-end connectivity testing with `ping`
