# MAC Address Tracing Lab: Frame Behavior Across Switches and Routers

## Objective
Trace the source/destination MAC address of a frame at every hop along three different paths through a multi-router topology, in order to demonstrate how MAC addresses change at each **router** (Layer 3) interface but stay unchanged across a **switch** (Layer 2), and verify the results using Packet Tracer's Simulation mode.

## Topology
![Network Topology](../labs-photos/day-12-life-of-a-packet.png)

| Network            | Segment                          |
|---------------------|-----------------------------------|
| 192.168.1.0/24      | PC1, PC2, PC3 LAN (via SW1)      |
| 192.168.12.0/24     | R1 – R2 link                      |
| 192.168.13.0/24     | R2 – R3 link                      |
| 192.168.3.0/24      | PC4, PC5, PC6 LAN (via SW2)      |

| Device | Interface | IP Address     |
|--------|-----------|-----------------|
| R1     | G0/1      | 192.168.1.254  |
| R1     | G0/0      | 192.168.12.1   |
| R2     | G0/1      | 192.168.12.2   |
| R2     | G0/0      | 192.168.13.2   |
| R3     | G0/1      | 192.168.13.3   |
| R3     | G0/0      | 192.168.3.254  |
| PC1    | NIC       | 192.168.1.1    |
| PC2    | NIC       | 192.168.1.2    |
| PC3    | NIC       | 192.168.1.3    |
| PC4    | NIC       | 192.168.3.1    |
| PC5    | NIC       | 192.168.3.2    |
| PC6    | NIC       | 192.168.3.3    |

## Key Concept
A frame's source/destination **MAC** address only changes when it crosses a **router** interface, because routing strips the old Layer 2 header and builds a new one for the next hop. A **switch** never rewrites the frame — it just forwards it — so the MAC pair stays identical across an entire switched segment. That means a switch (e.g. SW1 or SW2) never introduces a new source/destination pair of its own; it inherits whatever pair was used to reach or leave the router it's attached to.

Practical implication: to trace a frame across the whole path, only track the pair changing at each **router interface** (each physical interface has its own unique MAC), and treat "PC → Switch → Router" or "Router → Switch → PC" as one continuous segment.

## Part 1 — PC1 pings PC4
Trace the src/dst MAC address at each specified point along the path from PC1 to PC4.

| Segment                          | Source MAC | Destination MAC |
|------------------------------------|------------|-------------------|
| A. PC1 → SW1                      | `1111` (PC1 NIC) | `AAAA` (R1 G0/1 — PC1's gateway) |
| B. SW1 → R1 (G0/1)                | `1111` (PC1 NIC) | `AAAA` (R1 G0/1) |
| C. R1 (G0/0) → R2 (G0/1)          | `BBBB` (R1 G0/0) | `CCCC` (R2 G0/1) |
| D. R2 (G0/0) → R3 (G0/1)          | `DDDD` (R2 G0/0) | `EEEE` (R3 G0/1) |
| E. R3 (G0/0) → SW2                | `FFFF` (R3 G0/0) | `4444` (PC4 NIC) |
| F. SW2 → PC4                      | `FFFF` (R3 G0/0) | `4444` (PC4 NIC) |

A/B share the same frame (SW1 doesn't rewrite it), and E/F share the same frame (SW2 doesn't rewrite it). This is the exact mirror image of Part 3: the same six MACs appear, just with source and destination swapped at each hop, since it's the same four router interfaces and the same two host NICs, traveling in the opposite direction.

## Part 2 — PC1 pings PC3
Trace the src/dst MAC address at each specified point along the path from PC1 to PC3. Since PC1 and PC3 are on the same LAN (192.168.1.0/24), the frame never reaches a router — it's switched directly.

| Segment            | Source MAC     | Destination MAC |
|----------------------|-----------------|--------------------|
| A. PC1 → SW1         | `00D0.BA11.1111` (PC1 NIC) | `0010.1133.3333` (PC3 NIC) |
| B. SW1 → PC3         | `00D0.BA11.1111` (PC1 NIC) | `0010.1133.3333` (PC3 NIC) |

A and B are the same frame end-to-end — SW1 receives it on FastEthernet0/1 and simply switches it out FastEthernet0/3 to PC3, without rewriting the Ethernet header, since both hosts are on the same subnet and no routing occurs.

## Part 3 — PC4 pings PC1
Trace the src/dst MAC address at each specified point along the path from PC4 back to PC1.

| Segment                          | Source | Destination |
|------------------------------------|--------|--------------|
| PC4 → R3 (via SW2)                 | `4444` (PC4 NIC) | `FFFF` (R3 G0/0 — PC4's default gateway) |
| R3 → R2                            | `EEEE` (R3 G0/1) | `DDDD` (R2 G0/0) |
| R2 → R1                            | `CCCC` (R2 G0/1) | `BBBB` (R1 G0/0) |
| R1 → PC1 (via SW1)                 | `AAAA` (R1 G0/1) | `1111` (PC1 NIC) |

This confirms the reverse path is the mirror image of Part 1: the same four router interfaces are involved, just with source and destination swapped at each hop, and PC4/PC1's own NIC MACs bookend the trace.

## Methodology
1. Pinged once between the relevant hosts first, to let each device complete ARP and populate its MAC address table / ARP cache.
2. Re-sent the ping in Packet Tracer's **Simulation** mode.
3. Stepped through the simulation one device at a time, opening each PDU to inspect the Layer 2 (Ethernet) header's source and destination MAC fields.
4. Cross-referenced each MAC against `show interfaces <interface>` (or the device's config window) on the routers, and each PC's NIC properties, to identify which physical interface each MAC belonged to.

## Skills Demonstrated
- Differentiating Layer 2 (MAC/switching) behavior from Layer 3 (IP/routing) behavior
- Understanding why each router interface has its own unique MAC address
- Understanding that switches forward frames without rewriting MAC addresses
- Using Packet Tracer's Simulation mode and PDU inspection to trace a frame hop-by-hop
- Correlating ARP/MAC behavior with the underlying IP addressing and default gateway configuration
