# DHCP-Relay-Agent-Lab
Multi-site DHCP relay lab in Cisco Packet Tracer, configuring a router to forward DHCP requests from three branch networks back to a centralized server at Headquarters.

# DHCP Relay Agent Lab (Cisco Packet Tracer, Multi-Site DHCP Forwarding)

`Cisco Packet Tracer` · `DHCP Relay` · `ip helper-address` · `Router Configuration` · `Switching` · `CompTIA Labs`

## Overview
This lab was hands-on practice with DHCP relay across a multi-site network in Cisco Packet Tracer. Instead of putting a DHCP server on every branch's local subnet, this topology keeps DHCP centralized at Headquarters and forwards client requests from three separate branch sites back to that one server. The core skill here is configuring a router to relay broadcast DHCP traffic across routed boundaries, since DHCP discover and request packets don't normally leave a broadcast domain on their own.

## Objective
Get one central DHCP server at Headquarters handing out addressing automatically to clients on three different branch networks, none of which have their own local DHCP server. That means every router interface facing a branch has to be told to relay DHCP broadcasts to the HQ server instead of just dropping them.

## Environment
- Headquarters: `Server-PT` DHCP Server at `10.0.0.2`, on the `10.0.0.0/8` network, connected into the central router
- Central Router: `Router-PT`, with interfaces facing HQ and each branch (`Fa0/0`, `Fa1/0`, `Fa6/0`, `Fa7/0`, `Fa0/17`)
- Branch 1: on the `172.16.0.0/16` network, a `2960` switch feeding three end devices, `CLIENT_1`, `DHCP CLIENT_2`, and `DHCP CLIENT_3` (laptops), uplinked to the router
- Branch 2: on the `200.168.1.0/24` network, a single client PC (`C1`) connected straight back to the router
- Branch 3: on the `33.16.1.0/8` network, a `2960-24TT` switch feeding `PC0` and `Laptop0`, uplinked to the router
- Simulated in Cisco Packet Tracer, using both Realtime and Simulation mode

## Tools I Used

| Tool | What It Does | Why I Used It |
|------|--------------|----------------|
| Cisco Packet Tracer | Network simulation platform | Built and tested the full topology, HQ, router, three branch switches, and end devices, all in one virtual environment |
| DHCP Server (Server-PT) | DHCP service | Centralized address assignment for every branch instead of running a local DHCP server per site |
| Router (Router-PT) | Layer 3 routing and relay agent | Configured to forward DHCP broadcasts from each branch subnet back to the HQ server |
| Catalyst 2960 switches | Layer 2 switching | Connected branch end devices and carried traffic up to the router at each site |

## What I Did

### Laying Out the Topology
I built four sites in Packet Tracer: Headquarters with the DHCP server, Branch 1 with three clients behind a switch, Branch 2 with a single client PC, and Branch 3 with two devices behind a switch, all meeting at a central router. I gave each site its own addressing scheme, HQ on `10.0.0.0/8`, Branch 1 on `172.16.0.0/16`, Branch 2 on `200.168.1.0/24`, and Branch 3 on `33.16.1.0/8`, so this wasn't just one flat broadcast domain.

### Configuring the DHCP Server
I set up the DHCP service on the HQ server to hand out addressing for each branch's subnet. With relay in place, one server can serve multiple remote networks instead of needing a server at every site.

### Configuring DHCP Relay on the Router
On each router interface facing a branch network, I configured the interface to relay DHCP broadcasts toward the HQ server's address instead of letting them die at the local broadcast domain boundary. This is really the point of the whole lab. A router doesn't forward broadcast traffic by default, so without relay configured on each branch-facing interface, clients at Branch 1, 2, and 3 would just sit there sending DHCP discovers that never make it back to the HQ server.

### Verifying Client Addressing
I checked that clients at all three branches, including the ones labeled `DHCP CLIENT_2` and `DHCP CLIENT_3` at Branch 1, pulled an IP address automatically from the HQ server instead of needing static addressing or a local DHCP server. I also used Packet Tracer's Simulation mode to watch DHCP traffic actually cross the router between a branch and HQ, rather than just trusting that the clients showed a leased address in their IP config.

## What's in This Repo

```
dhcp-relay-lab/
├── README.md                       # This file
├── configs/
│   └── router-relay-config.txt     # Router relay/interface configuration
└── screenshots/
    ├── 01-topology-overview.png
    ├── 02-dhcp-server-config.png
    ├── 03-branch1-client-addressing.png
    ├── 04-branch2-client-addressing.png
    ├── 05-branch3-client-addressing.png
    └── 06-simulation-dhcp-traffic.png
```

## Skills I Picked Up
- Understanding why DHCP needs help crossing subnets, since DHCP discover and request messages are broadcasts, and routers don't forward broadcast traffic between subnets by default.
- Configuring relay on a per interface basis, and realizing each branch facing interface on the router needed its own relay configuration pointing back at the HQ server, not just one global setting.
- Centralizing services instead of duplicating them, and seeing firsthand why an organization would run one DHCP server at HQ instead of standing up a separate server at every branch.
- Using Simulation mode to actually prove behavior instead of just assuming it, by watching the DHCP packet exchange hop from branch to HQ and back rather than only checking the end result on the client.

## How This Applies in the Real World
Almost no real organization runs a DHCP server on every branch LAN. It's far more common to centralize DHCP, and often other services, at a hub site and relay client requests to it from every remote location. This is exactly the kind of setup you'd find in a small to midsize company with a headquarters and a handful of branch offices, and it's a good example of how a design decision made for administrative convenience, one DHCP server to manage instead of several, has a direct networking consequence. Someone has to configure relay correctly on every router in between, or remote users simply won't get an address.

## Where I'm Coming From
I'm making the jump into cybersecurity from a background in healthcare. It's a different field on paper, but a lot of the muscle memory carries over: following procedures carefully, protecting sensitive information, staying calm and methodical when something isn't working the way it's supposed to. I'm currently studying for CompTIA Security+ and building labs like this one to get real hands-on reps in with core networking concepts, since a lot of security work assumes you already understand how the underlying network behaves.

## What I Want to Learn Next
- Digging into the actual `ip helper-address` (or equivalent) configuration syntax in more depth, including how it behaves with multiple DHCP scopes on one server
- Practicing DHCP relay in a setup with overlapping or misconfigured scopes to see how the router and server behave when something's wrong
- Adding a secondary DHCP server at HQ for redundancy and testing failover behavior
- Capturing and reading the actual DHCP packet exchange (discover, offer, request, ack) in Simulation mode in more detail, rather than just confirming clients got an address

## Limitations & What I'd Do Differently in Production
- This was a simulated environment, so real world concerns like DHCP server high availability, rogue DHCP detection, and DHCP snooping weren't part of the exercise.
- Only one DHCP server was used. A production deployment serving multiple remote sites would typically want redundancy so a single server outage doesn't take down addressing for every branch at once.
- No security hardening was applied to the DHCP relay setup itself, for example restricting which addresses can act as a relay target, or DHCP snooping on the branch switches. This lab was focused purely on getting relay working, not on hardening it.

## References
- [Cisco DHCP Relay Agent Overview](https://www.cisco.com/c/en/us/support/docs/ip/dynamic-address-allocation-resolution/13567-dhcpre.html)
- [CompTIA Security+ (SY0-701) Exam Objectives](https://www.comptia.org/certifications/security)
- Cisco Packet Tracer, simulation environment used throughout
