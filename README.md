# Week 7 — Enterprise VPN, Firewall, and Secure Access Implementation
Network Administration Internship | IT-Simplera Solutions

## Overview
Design, deployment, and verification of a secure branch-to-branch enterprise network in Cisco GNS3, connecting two branch routers over a site-to-site IPsec VPN, protected by a Zone-Based Firewall, hardened with AAA/SSH device management, secured routing via OSPF authentication, and monitored through centralized Syslog, NTP, and SNMPv3.

## Topology
- **R-A (Branch A):** WAN-edge/gateway router, LAN 192.168.10.0/24, WAN peer 203.0.113.1
- **R-B (Branch B):** WAN-edge/gateway router, peer end of the IPsec tunnel to R-A
- **PC-A / PC-B:** End hosts on each branch LAN, used for connectivity and firewall policy verification
- **Monitoring Server:** 192.168.10.100 — runs rsyslog, chrony (NTP), and snmpd (SNMPv3), reachable from R-A across the ZBF

2 Cisco routers (R-A, R-B), 2 end-host PCs, and 1 Linux monitoring server.

## Technologies Implemented
- **IPsec Site-to-Site VPN** — ISAKMP (IKE Phase 1), IPsec Phase 2, pre-shared keys, transform sets, and crypto maps establishing an encrypted tunnel between R-A and R-B
- **Zone-Based Firewall (ZBF)** — security zones, zone pairs, class maps, policy maps, and service policies enforcing bidirectional traffic inspection between branches
- **AAA Authentication + SSHv2** — local user database, encrypted passwords, Telnet disabled, secure console/VTY access, login banner (MOTD)
- **OSPF Authentication** — authenticated routing adjacency between R-A and R-B, with passive interfaces and prefix-list route filtering
- **Syslog / NTP / SNMPv3** — centralized logging, authenticated+encrypted time synchronization (chrony), and SNMPv3 (authPriv, SHA/AES) monitoring of router system data
- **Basic Hardening** — unused interfaces/ports shut down, unnecessary services disabled, administrative access restricted

## Verification Highlights
- IPsec tunnel established and encrypted PC-A ↔ PC-B communication confirmed
- ZBF policies verified in both directions without blocking legitimate traffic
- SSH + AAA login verified from both R-A and R-B, including privileged EXEC access
- OSPF neighbor adjacency authenticated and stable
- NTP: `show ntp associations` / `show ntp status` confirm R-A synchronized to the monitoring server (stratum 11, sys.peer selected)
- SNMPv3: `snmpwalk -v3 -l authPriv -a SHA -x AES` against R-A returns full system MIB data (sysDescr, sysUpTime, sysName, etc.)
- Syslog: rsyslogd active and receiving router log messages on port 514

## Troubleshooting Notes
An NTP sync issue (`reach` stuck at 0 despite visible traffic) was isolated using `tcpdump` on the monitoring server, confirming NTP request/response packets were passing correctly at the network level — the issue was a display/timing artifact, resolved once the client polling interval completed a full cycle.

## Repository Contents
- `/Screenshots` — Verification outputs (VPN, ZBF, SSH/AAA, OSPF auth, Syslog, NTP, SNMP)
- `Week7-1.gns3` — GNS3 project file
- `Report.pdf` — Full project documentation, including the troubleshooting report
- `/Router Configuration files`-

## Author
Ayachi Med Ali — Network Administration Intern, IT-Simplera Solutions
