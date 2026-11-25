---
title: CCNA & Networking Protocols Study Guide
aliases:
  - Networking Interview Prep
  - CCNA Notes
tags:
  - networking
  - ccna
  - protocols
  - interview-prep
status: complete
---

# CCNA & Networking Protocols Study Guide

> [!info] About This Note
> This study guide consolidates core CCNA concepts and essential networking protocols into a structured Obsidian reference.Questions use collapsible callouts (`> [!question]-`) so you can test yourself before revealing the answer.

---

## Part 1: CCNA Core Concepts

### Section 1: Networking Fundamentals

> [!question]- 1.How is a computer network defined?
> A network consists of two or more interconnected devices configured to exchange data and share hardware or software resources.

> [!question]- 2.What are the primary categories of networks by geographic scope?
> - **PAN** (Personal Area Network)
> - **LAN** (Local Area Network)
> - **WLAN** (Wireless Local Area Network)
> - **MAN** (Metropolitan Area Network)
> - **WAN** (Wide Area Network)


> [!question]- 3.What are the OSI and TCP/IP models?  
> - **[[OSI Model]] (7 Layers):**
>   1. **Physical**
>   2. **Data Link**
>   3. **Network**
>   4. **Transport** (TCP/UDP)
>   5. **Session**
>   6. **Presentation**
>   7. **Application**
> - **[[TCP-IP Model]] (4 Layers):** Application, Transport, Internet, and Network Access.
> - **Troubleshooting Tip:** Analysis typically starts from the lower layers (Layer 1) and moves upward.


> [!question]- 4.How do [[TCP]] and [[UDP]] differ?
> - **[[TCP]]:** Establishes a connection before transmitting, guarantees delivery, and preserves packet order.
> - **[[UDP]]:** Operates without a formal handshake, prioritizing low latency and speed over guaranteed delivery.

> [!question]- 5.What is a [[MAC Address]]?
> A permanent 48-bit physical identifier burned into a network interface card (NIC), written in hexadecimal (e.g., `00:1A:2B:3C:4D:5E`).

> [!question]- 6.What is an [[IP Address]]?
> A logical network-layer identifier assigned to a host for routing purposes.[[IPv4]] uses 32 bits, while [[IPv6]] uses 128 bits.

> [!question]- 4.What is a Subnet Mask, CIDR, VLSM, and Private IP Ranges?  
> - **Subnet Mask:** Separates the network portion and host portion of an IP address (e.g., `255.255.255.0` = `/24`). The **Network ID** is calculated using a **bitwise AND** operation between the IP address and the subnet mask.
> - **[[CIDR]] (Classless Inter-Domain Routing):** Allows variable subnet masks using slash notation (e.g., `192.168.1.0/24`).
> - **[[VLSM]] (Variable Length Subnet Mask):** Enables efficient IP address utilization by subnetting a network into differently sized subnets (e.g., splitting `192.168.1.0/24` into four `/26` subnets: `.0/26`, `.64/26`, `.128/26`, `.192/26`).
> - **Private IP Ranges:**
>   - `10.0.0.0/8`
>   - `172.16.0.0/12`
>   - `192.168.0.0/16`

> [!question]- 5.What is a Default Gateway?  
> - The router IP used to send traffic destined for networks outside the local subnet.
> - Represented by the `0.0.0.0/0` route in the routing table; when multiple gateways exist, **route metrics** determine priority.
> - **Commands:** `route print` (CMD) and `Get-NetRoute` (PowerShell).

> [!question]- 6.What is DNS, why is it critical for Active Directory, and what are DNS Zone Types?  
> - **[[DNS]] (Domain Name System):** Resolves domain names/hostnames to IP addresses (Port `53`).
> - **Criticality for [[Active Directory]]:** AD is tightly integrated with DNS and relies on it to locate Domain Controllers, services, and resources. Without proper DNS configuration, AD will not function correctly.
> - **DNS Zone Types:**
>   - **Primary Zone:** Read/write copy of the zone database.
>   - **Secondary Zone:** Read-only copy updated via zone transfer.
>   - **Stub Zone:** Contains only Name Server (`NS`) records.
>   - **AD-Integrated Zone:** Stored in Active Directory and supports secure dynamic updates.
> - **Lookup & Forwarding:**
>   - **Forward Lookup Zones:** Resolve names → IP addresses.
>   - **Reverse Lookup Zones:** Resolve IP addresses → names.
>   - **Conditional Forwarders:** Forward queries for specific domains.
> - **Zone Transfer Types:** Full (`AXFR`) and Incremental (`IXFR`).

> [!question]- 7.What is DHCP and how is it configured on Windows Server?  
> - **[[DHCP]] (Dynamic Host Configuration Protocol):** Automatically assigns IP addresses and network configuration parameters to clients (Ports `67/68`).
> - **Windows Server Installation:**
>   ```powershell
>   Install-WindowsFeature DHCP
>   ```
> - **Key Configuration Details:**
>   - A **Scope** defines the IP range, subnet mask, default gateway, and DNS servers.
>   - **Default Lease Duration:** 8 days.
>   - **Reservations:** Assign a fixed IP address based on a device's **MAC address**.
>   - Must be **authorized in Active Directory**.
>   - Supports **Failover** and **Load Balancing**.

---

### Section 2: IP Addressing & Subnetting

> [!question]- 11.What is [[Subnetting]]?
> The practice of partitioning a larger IP network into smaller, more manageable sub-networks to improve organization, security, and address utilization.

> [!question]- 12.What is the purpose of the loopback address?
> `127.0.0.1` (and the `127.0.0.0/8` range) routes traffic back to the local host to verify that the local TCP/IP stack is functioning properly.

> [!question]- 13.What is [[APIPA]]?
> Automatic Private IP Addressing assigns a link-local IP in the `169.254.0.0/16` range when a client fails to reach a [[DHCP]] server.

> [!question]- 14.How do public and private IP addresses differ?
> - **Public IPs** are globally unique and routable across the public internet.
> - **Private IPs** are restricted to internal local networks and require translation to reach the internet.

> [!question]- 15.What is NAT, its types, and pros/cons?  
> - **[[NAT]] (Network Address Translation):** Converts private IP addresses into public IP addresses; configured on routers or firewalls (e.g., **Azure NAT Gateway**).
> - **Types:**
>   - **Static NAT:** 1:1 mapping between a private IP and a public IP.
>   - **Dynamic NAT:** Maps private IPs to a pool of public IPs.
>   - **[[PAT]] / NAPT (NAT Overload):** Port-based translation allowing many private IPs to share one public IP.
> - **Advantages:** Conserves public IPv4 addresses and improves security.
> - **Disadvantages:** Potential issues with **VoIP** and **peer-to-peer (P2P)** applications.

> [!question]- 16.What is IPv6 and how does it differ from IPv4?  
> | Feature | [[IPv4]] | [[IPv6]] |
> | :--- | :--- | :--- |
> | **Address Size** | 32-bit (~4.3 billion addresses) | 128-bit (virtually unlimited) |
> | **Format** | Dotted decimal (`192.168.0.1`) | Hexadecimal (`2001:db8::1`) |
> | **Auto-Config & NAT** | Relies on DHCP / NAT | Supports **SLAAC**; **NAT** is not required |
> | **Resolution & Broadcast** | Uses **ARP** & Broadcast | Uses **Neighbor Discovery**; Multicast only (no broadcast) |
> | **Security** | Optional IPsec | **IPsec** is built-in |
> - **Dual-Stack:** Environments running IPv4 and IPv6 simultaneously.

---

### Section 3: Switching (Layer 2)

> [!question]- 17.What does a network [[Switch]] do?
> A Layer 2 device that learns [[MAC Address|MAC addresses]] and selectively forwards Ethernet frames out the appropriate port.

> [!question]- 18.What is a VLAN, its benefits, and Trunk vs Access ports?  
> - **[[VLAN]] (Virtual Local Area Network):** Logically segments a network at **Layer 2** (VLAN IDs range from `1` to `4094`).
> - **Benefits:** Improves security by isolating broadcast domains, reduces broadcast traffic, and boosts efficiency.
> - **Port Types:**
>   - **Access Ports:** Carry a single VLAN.
>   - **Trunk Ports:** Carry multiple VLANs across one link using **802.1Q** tagging.
> - **Inter-VLAN Routing:** Requires a **Layer 3 switch** or **router**.

> [!question]- 19.What is [[VTP]]?
> VLAN Trunking Protocol is a Cisco-proprietary mechanism for centrally propagating and synchronizing VLAN configurations across switches in the same domain.

> [!question]- 20.What is [[STP]]?
> Spanning Tree Protocol prevents Layer 2 switching loops and broadcast storms by selectively blocking redundant paths.

> [!question]- 21How does [[RSTP]] improve upon STP?
> Rapid Spanning Tree Protocol significantly reduces the time required for a switched network to converge after a topology change.

> [!question]- 22.What is a [[BPDU]]?
> A Bridge Protocol Data Unit is a control frame exchanged between switches running [[STP]] to elect a root bridge and calculate loop-free paths.

> [!question]- 23.What is [[Port Security]]?
> A Layer 2 security feature that restricts which and how many MAC addresses are permitted to communicate through a specific switch port.

> [!question]- 24.What is [[EtherChannel]]?
> Link aggregation technology that combines multiple physical switch links into a single logical channel for increased bandwidth and redundancy.

---

### Section 4: Routing (Layer 3)

> [!question]- 25.What is the role of a [[Router]]?
> A Layer 3 device that examines destination IP addresses and forwards packets between different networks based on its routing table.

> [!question]- 26.What distinguishes static routing from dynamic routing?
> - **Static Routing:** Paths are manually entered and maintained by a network administrator.
> - **Dynamic Routing:** Routers automatically discover, share, and update paths using routing protocols.

> [!question]- 27.What is [[RIP]]?
> Routing Information Protocol is a distance-vector routing protocol that measures path cost by hop count, with a maximum limit of **15 hops**.

> [!question]- 28.How do RIPv1 and RIPv2 compare?
>
> | Feature | RIPv1 | RIPv2 |
> | :--- | :--- | :--- |
> | Addressing | Classful | Classless |
> | VLSM Support | No | Yes |
> | Authentication | Not supported | Supported |

> [!question]- 29.What is [[OSPF]]?
> Open Shortest Path First is an open-standard link-state routing protocol that uses interface **cost** as its metric and computes the shortest loop-free path with **Dijkstra's SPF** algorithm.

> [!question]- 30.What are the 5 [[OSPF]] packet types?
> 1.**Hello**
> 2.**DBD** (Database Description)
> 3.**LSR** (Link-State Request)
> 4.**LSU** (Link-State Update)
> 5.**LSAck** (Link-State Acknowledgment)

> [!question]- 31.What is [[EIGRP]] and what algorithm does it use?
> Enhanced Interior Gateway Routing Protocol is a Cisco-originated advanced distance-vector (hybrid) protocol that primarily evaluates **bandwidth** and **delay**.It relies on **DUAL** (Diffusing Update Algorithm) to guarantee loop-free convergence.

> [!question]- 32.What is [[BGP]]?
> Border Gateway Protocol is the exterior gateway protocol responsible for exchanging routing information between different Autonomous Systems (ASes) across the internet.

---

### Section 5: WAN Technologies

> [!question]- 33.What are [[HDLC]] and [[PPP]]?
> - **HDLC:** Cisco's default Layer 2 encapsulation for synchronous serial WAN links.
> - **PPP (Point-to-Point Protocol):** An open-standard serial link protocol supporting authentication and multi-protocol transport.

> [!question]- 34.What is [[MPLS]]?
> Multiprotocol Label Switching accelerates packet forwarding across service provider networks by making forwarding decisions based on short path labels rather than full IP routing table lookups.

> [!question]- 35.What is a [[GRE Tunnel]]?
> Generic Routing Encapsulation wraps one network protocol's packets inside another to create point-to-point logical tunnels across an intermediate network.

> [!question]- 36.What is a VPN and what types/protocols exist?  
> - **[[VPN]] (Virtual Private Network):** Creates a secure encrypted tunnel over the internet.
> - **Deployment Models:** Site-to-Site, Remote Access, **DMVPN** (scalable multi-site), and **Always On VPN** (modern replacement for DirectAccess).
> - **Common Protocols:**
>   - **PPTP:** Legacy/deprecated, weak security.
>   - **L2TP/IPsec:** Layer 2 tunneling paired with IPsec encryption.
>   - **SSTP:** HTTPS-based VPN protocol.
>   - **IKEv2:** Mobile-friendly and resilient.
>   - **OpenVPN:** Third-party open-source VPN.
> - **Tunneling & Auth:** Supports **split tunneling** or **full tunneling**; authentication is often handled via **[[RADIUS]] (NPS)**.

> [!question]- 37.What is IPsec and how is it configured?  
> - **[[IPsec]]:** Secures IP traffic through encryption and authentication (widely used for VPNs).
> - **Protocols:** **AH** (Authentication Header) and **ESP** (Encapsulating Security Payload).
> - **Modes:** **Transport Mode** and **Tunnel Mode**.
> - **Key Exchange:** Handled by **IKE**.
> - **Windows Configuration:** Done via **Windows Firewall with Advanced Security** or **Group Policy**.

> [!question]- 38.What is [[DMVPN]]?
> Dynamic Multipoint VPN is a scalable Cisco architecture that allows multiple branch sites to build dynamic mesh tunnels without manually configuring every peer-to-peer connection.

> [!question]- 39.What are [[Metro Ethernet]] and [[Frame Relay]]?
> - **Metro Ethernet:** High-speed Carrier Ethernet service used for metropolitan and WAN connectivity.
> - **Frame Relay:** A legacy packet-switched WAN technology largely superseded by MPLS and modern broadband/Ethernet links.

---

### Section 6: Security & First-Hop Redundancy

> [!question]- 40.What is an [[ACL]] (Access Control List)?
> A sequential set of permit or deny rules applied to a router or switch interface to filter network traffic.

> [!question]- 41.What are the two main types of [[ACL]]?
> - **Standard ACL:** Filters traffic based solely on the **source IP** address.
> - **Extended ACL:** Filters traffic based on **source IP, destination IP, protocol, and port numbers**.

> [!question]- 42.What is [[DHCP Snooping]]?
> A Layer 2 switch security feature that distinguishes between trusted and untrusted ports to block rogue [[DHCP]] servers and prevent DHCP spoofing attacks.

> [!question]- 43.What is [[DAI]] (Dynamic ARP Inspection)?
> A Layer 2 security mechanism that validates [[ARP]] packets against the DHCP snooping binding table to prevent ARP spoofing and man-in-the-middle attacks.

> [!question]- 44.What is AAA, and what is the difference between Authentication and Authorization?  
> - **[[AAA]]:** Stands for **Authentication, Authorization, and Accounting**.
>   - **Authentication:** Verifies identity (*who you are*).
>   - **Authorization:** Determines access rights (*what you are allowed to do*).
>   - **Accounting:** Tracks and logs user activity (*what you did*).

> [!question]- 45.How do [[RADIUS]] and [[TACACS+]] compare?
>
> | Feature | RADIUS | TACACS+ |
> | :--- | :--- | :--- |
> | Standard | Open standard | Cisco-proprietary |
> | Transport Protocol | [[UDP]] | [[TCP]] |
> | Primary Use | Network access AAA | Device administration AAA |

> [!question]- 45.How do [[HSRP]] and [[VRRP]] compare?
> - **HSRP (Hot Standby Router Protocol):** Cisco-proprietary First-Hop Redundancy Protocol (FHRP) providing default gateway failover.
> - **VRRP (Virtual Router Redundancy Protocol):** Open-standard FHRP providing automatic default gateway redundancy across multi-vendor routers.

---

### Section 7: Advanced Routing & Services

> [!question]- 46.What are [[OSPF]] DR and BDR?
> The **Designated Router (DR)** and **Backup Designated Router (BDR)** manage LSA flooding on multi-access broadcast networks to minimize full-mesh adjacency overhead.

> [!question]- 47.How is an [[OSPF]] DR elected?
> 1.Highest OSPF interface **priority** (default is `1`; `0` opts out of election).
> 2.Tie-breaker: Highest **Router ID**.

> [!question]- 48.What is [[Route Redistribution]]?
> The process of advertising and exchanging routing information between two different routing protocols (e.g., between OSPF and EIGRP).

> [!question]- 49.What is a [[Stub Area]] in OSPF?
> A specialized OSPF area that blocks external LSAs (Type 5) from entering, reducing routing table size and memory/CPU overhead on area routers.

> [!question]- 50.What is [[Route Summarization]]?
> Aggregating multiple contiguous subnets into a single summary prefix advertisement to shrink routing tables and contain topology instability.

> [!question]- 51.What is a [[Floating Static Route]]?
> A backup static route configured with a higher **Administrative Distance (AD)** than the primary route so it only enters the routing table if the primary path fails.

> [!question]- 52.What are the default [[BGP]] Administrative Distances?
> - **External BGP (eBGP):** `20`
> - **Internal BGP (iBGP):** `200`

> [!question]- 53.How does [[BGP]] select the best path (metric)?
> Rather than a single numeric metric, BGP evaluates **path attributes**—including `AS_PATH`, `LOCAL_PREF`, and `MED`.

> [!question]- 54.What is the [[MPLS]] 32-bit label header format?
> - **Label:** 20 bits
> - **EXP (Traffic Class / QoS):** 3 bits
> - **S (Bottom of Stack flag):** 1 bit
> - **TTL (Time to Live):** 8 bits

> [!question]- 55.What is QoS, and what is Policing vs Shaping?  
> - **[[QoS]] (Quality of Service):** Prioritizes network traffic to ensure performance for critical apps like **VoIP** and **video**.
> - **Marking:** Traffic is marked using **DSCP** values.
> - **Techniques:** **Policing** drops excess traffic; **Shaping** delays (buffers) excess traffic.
> - **Windows Configuration:** Configured via **Group Policy** or PowerShell (`New-NetQosPolicy`).

---

### Section 8: Focused Concepts & Monitoring

> [!question]- 56.What are NetFlow, IPFIX, and sFlow?  
> - **[[NetFlow]]:** Cisco-developed protocol that collects network traffic statistics for analysis.
> - **IPFIX:** The standardized version of NetFlow.
> - **sFlow:** Uses packet sampling for traffic monitoring.
> - **Use Case:** Provides visibility into traffic patterns, bandwidth usage, and security events; feeds into monitoring and **SIEM** systems.

> [!question]- 57.What is [[Syslog]]?
> A standard logging protocol used by routers, switches, and servers to send event and diagnostic log messages to a centralized log server.

> [!question]- 58.What is [[Port Mirroring]] ([[SPAN]])?
> Switched Port Analyzer copies traffic from one or more source ports or VLANs to a designated destination port for packet capture and monitoring (e.g., Wireshark or IDS).

> [!question]- 59.What is [[CoPP]] (Control Plane Policing)?
> A security mechanism that rate-limits and filters traffic destined for a router's CPU to protect the control plane from DoS attacks and excessive traffic.

> [!question]- 60.What are Jumbo Frames?  
> - Ethernet frames with an **MTU** larger than the standard `1500` bytes (typically **~9000 bytes**).
> - **Benefits & Use Cases:** Improves performance by reducing packet overhead; common in **iSCSI**, **NFS**, and **backup traffic**.
> - **Requirement:** Every device along the network path must support Jumbo Frames.
> - **Test Command:** `ping -l 8972 -f <target>`

> [!question]- 61.What is the difference between the [[Data Plane]] and the [[Control Plane]]?
> - **Data Plane (Forwarding Plane):** Responsible for the high-speed forwarding of packets from ingress to egress interfaces.
> - **Control Plane:** Responsible for building routing tables, exchanging routing protocol updates, and making forwarding decisions.

> [!question]- 61.What is [[Convergence]]?
> The time it takes for all routers in a network to share topology updates, recalculate paths, and reach a consistent view of the network after a change.

---

## Part 2: Core Networking Protocols & Reference

### Transport & Internet Layer Comparisons

#### [[TCP]] vs.[[UDP]]

| Characteristic    | TCP                                                      | UDP                                               |
| :---------------- | :------------------------------------------------------- | :------------------------------------------------ |
| Reliability       | Guaranteed delivery with error checking & retransmission | Best-effort, no acknowledgments or retransmission |
| Connection Model  | Connection-oriented                                      | Connectionless                                    |
| Packet Ordering   | Preserves exact sequence                                 | No ordering guarantees                            |
| Speed / Latency   | Slower due to overhead                                   | Faster, minimal latency                           |
| Typical Use Cases | File downloads, banking, web browsing                    | Voice/video calls, live streaming, online gaming  |
|                   |                                                          |                                                   |

---

### Web, File Transfer & Email Protocols

#### [[HTTP]] vs.[[HTTPS]]

| Characteristic | HTTP | HTTPS |
| :--- | :--- | :--- |
| Encryption | Plaintext (unencrypted) | Encrypted via [[SSL]]/[[TLS]] |
| Default Port | `80` | `443` |
| Security Level | Vulnerable to interception | Protected confidentiality & integrity |

#### [[POP3]] vs.[[IMAP]]

| Characteristic | POP3 | IMAP |
| :--- | :--- | :--- |
| Email Handling | Downloads messages to the client | Synchronizes messages across devices |
| Primary Storage | Local device | Mail server |
| Default Port | `110` | `143` |

- **[[FTP]] (Port 21):** Standard protocol for transferring files between client and server (e.g., publishing web assets).
- **[[SFTP]] (Port 22):** Encrypted file transfer running over an [[SSH]] session.
- **[[TFTP]] (Port 69):** Minimalist, UDP-based file transfer protocol often used for network device firmware updates.
- **[[SMTP]] (Port 25):** Handles outgoing email transmission between clients and mail servers.

---

### Management, Security & Discovery Protocols

- **[[ARP]]:** Resolves a known Layer 3 IP address into its corresponding Layer 2 MAC address on a local segment.
- **[[ICMP]]:** Delivers network error reporting and diagnostic utilities such as `ping`.
- **[[SSH]] (Port 22) vs.[[Telnet]] (Port 23):** Both provide remote command-line access, but **SSH** encrypts all session traffic while **Telnet** transmits credentials and data in plaintext.
- **[[SNMP]] (Ports 161/162):** Monitors and manages network infrastructure devices like switches and routers.
- **[[NTP]] (Port 123):** Keeps system clocks synchronized across servers and network hardware.
- **[[LDAP]] (Port 389):** Queries and manages centralized directory services and user authentication (such as Active Directory).
- **[[SSL]] & [[TLS]]:** Cryptographic protocols for securing network communications; **TLS** is the modern, secure successor to the deprecated **SSL**.

---

### Real-Time, WAN, IoT & Web API Protocols

- **[[SIP]] (Port 5060 / 5061 TLS):** Establishes, modifies, and terminates VoIP and multimedia sessions.
- **[[RTP]]:** Transports real-time audio and video streams (e.g., video conferencing).
- **[[PPPoE]]:** Encapsulates PPP frames inside Ethernet frames, widely used in DSL broadband access.
- **[[L2TP]]:** Layer 2 Tunneling Protocol; creates tunnels and is typically paired with [[IPsec]] for encryption.
- **[[PPTP]]:** An outdated VPN tunneling protocol avoided today due to known security weaknesses.
- **[[SCTP]]:** Stream Control Transmission Protocol; a transport protocol commonly utilized in telecommunications signaling.
- **[[MQTT]]:** Lightweight publish/subscribe messaging protocol tailored for IoT sensors and devices.
- **[[CoAP]]:** Constrained Application Protocol built for efficient communication on low-power IoT hardware.
- **[[WebSocket]]:** Enables persistent, bidirectional (full-duplex) communication between client and server (e.g., live chat, real-time financial tickers).
- **[[SOAP]]:** Protocol using XML formatting for structured web service exchanges, common in enterprise and banking integrations.
- **[[REST]]:** Architectural style for web APIs leveraging standard HTTP verbs (`GET`, `POST`, `PUT`, `DELETE`) to operate on resources (e.g., `GET /products`, `POST /orders`).

---

### Quick Reference: Common Protocol Port Numbers

| Protocol | Default Port(s) | Primary Purpose |
| :--- | :--- | :--- |
| **FTP** | `21` | File transfer control |
| **SSH / SFTP** | `22` | Secure remote access & secure file transfer |
| **Telnet** | `23` | Unencrypted remote login |
| **SMTP** | `25` | Sending email |
| **DNS** | `53` | Domain name resolution |
| **DHCP** | `67` / `68` | Automatic IP assignment (Server / Client) |
| **TFTP** | `69` | Lightweight UDP file transfer |
| **HTTP** | `80` | Unencrypted web traffic |
| **POP3** | `110` | Retrieving email locally |
| **NTP** | `123` | Clock synchronization |
| **IMAP** | `143` | Synchronized server-side email |
| **SNMP** | `161` / `162` | Network monitoring & traps |
| **LDAP** | `389` | Directory services & authentication |
| **HTTPS** | `443` | Encrypted web traffic |
| **SMB** | `445` | Network file & printer sharing |
| **RDP** | `3389` | Remote Desktop access |
| **SIP** | `5060` | VoIP session signaling |
| **SIP (TLS)** | `5061` | Encrypted VoIP session signaling |

---

## Section 9 – Active Directory

> [!question]- What is a Domain?
> A logical boundary in [[Active Directory]] that defines a security and administrative scope. All objects within a domain share the same AD database, security policies, and trust relationships. Identified by DNS names (e.g., `company.local`).

> [!question]- What is a Forest?
> The highest-level logical container in [[Active Directory]]. Consists of one or more domains that share a common schema, configuration, and global catalog. Trust relationships between domains in the same forest are **automatic and transitive**.

> [!question]- What is an Organizational Unit (OU)?
> A container used to organize objects (users, computers, groups) within a domain. Enables **delegation of administrative control** and **targeted Group Policy application**.
> > [!note] OUs do **not** provide security boundaries like domains.

> [!question]- What is the Global Catalog (GC)?
> A special Domain Controller role that stores a **partial, read-only copy** of all objects in the forest. Used for user logon (especially in multi-domain environments) and resolving **universal group memberships**.

> [!question]- What is Kerberos authentication?
> The default authentication protocol in [[Active Directory]]. Uses **tickets** and a trusted third party—the **Key Distribution Center (KDC)** on the DC—to authenticate users securely without transmitting passwords over the network.

> [!question]- What is a Trust Relationship?
> Allows users in one domain to access resources in another domain. Trusts can be **one-way or two-way**, and **transitive or non-transitive**. Within a forest, trusts between domains are created automatically.

> [!question]- What are FSMO roles?
> **Flexible Single Master Operations** roles are specialized tasks assigned to specific Domain Controllers to prevent conflicts in critical AD operations:
> - **Forest-wide:** Schema Master, Domain Naming Master
> - **Domain-wide:** RID Master, PDC Emulator, Infrastructure Master

> [!question]- What is the PDC Emulator role?
> Handles **time synchronization**, **password changes**, **account lockouts**, and legacy compatibility. It is the most critical [[FSMO]] role and should be placed on a highly available Domain Controller.

> [!question]- What is Group Policy?
> A feature used to centrally manage user and computer settings in [[Active Directory]]. Policies are applied through **Group Policy Objects (GPOs)** linked to sites, domains, or OUs.

> [!question]- How does Group Policy processing work?
> Processed in the **LSDOU** order:
> `Local` → `Site` → `Domain` → `OU`
> If multiple policies conflict, the **last applied policy takes precedence** unless **Enforcement** or **Block Inheritance** is configured.

> [!question]- What is Group Policy Inheritance?
> Allows child OUs to inherit GPOs from parent containers. Inheritance can be **blocked** at the OU level, while specific GPOs can be **enforced** to override blocking.

> [!question]- What is SYSVOL?
> A shared directory on Domain Controllers that stores **Group Policy templates** and **logon scripts**. Replicated between DCs using **DFS Replication (DFSR)**.

> [!question]- What is AD Replication?
> Ensures consistency of directory data across all Domain Controllers using **multi-master replication**, configurable for sites, schedules, and costs.

> [!question]- What are AD Sites and Services?
> AD Sites represent **physical network locations**. They optimize replication traffic and client authentication by directing clients to the **nearest Domain Controller** based on IP subnets.

> [!question]- What is a Security Group vs a Distribution Group?
> - **Security Groups:** Used to assign permissions to resources.
> - **Distribution Groups:** Used only for email distribution; **cannot** be used for access control.

> [!question]- What is a Universal Group?
> - Can contain users and groups from **any domain in the forest** and be assigned permissions in any domain.
> - Membership changes are replicated through the **[[Global Catalog]]**.

> [!question]- What is Delegation of Control?
> - Allows administrators to assign specific administrative tasks (such as password resets) to non-admin users without granting full **Domain Admin** privileges.

> [!question]- What is a Read-Only Domain Controller (RODC)?
> - Hosts a **read-only copy** of Active Directory.
> - Deployed in **branch offices** to improve security by preventing unauthorized changes and limiting credential exposure.

> [!question]- What is the AD Schema?
> - Defines all **object classes** and **attributes** in Active Directory.
> > [!warning] Schema changes affect the **entire forest** and must be performed carefully.

> [!question]- What is a Fine-Grained Password Policy (FGPP)?
> - Allows applying different password and account lockout policies to specific users or groups within the **same domain**, overriding the default domain policy.


> [!question]- What is LDAP / LDAPS?  
> - **[[LDAP]] (Lightweight Directory Access Protocol):** Protocol used to query and modify directory services such as **Active Directory** (e.g., storing user information and handling authentication).
> - **Ports:** `389` (LDAP) and `636` (LDAPS – Secure LDAP).

> [!question]- What is ARP?  
> - **[[ARP]] (Address Resolution Protocol):** Maps IPv4 addresses to **MAC addresses** (e.g., `192.168.1.5` → `00:11:22:33:44:55`).
> - **Behavior:** ARP requests are **broadcast**; ARP replies are **unicast**.
> - **Gratuitous ARP:** Detects IP address conflicts.
> - **Security:** Mitigate ARP spoofing with static ARP entries and **Dynamic ARP Inspection (DAI)**.
> - **Command:** `arp -a` (views ARP cache).


---

## Section 10 – Windows Server Administration

> [!question]- How is Windows Server Backup configured?
> Built-in backup solution in Windows Server.
> - **Install:**
>   ```powershell
>   Install-WindowsFeature Windows-Server-Backup
>   ```
> - **Supported Types:** Full Server, System State, Custom, and Bare Metal Recovery.
> - **Key PowerShell Commands:** `New-WBPolicy`, `Add-WBSystemState`, `Start-WBBackup`, and `Start-WBSystemStateRecovery` (for system state recovery). Supports scheduled backups.

> [!question]- What is an IIS Application Pool?
> An isolated environment in [[IIS]] where web applications run; each pool uses its own worker process (`w3wp.exe`).
> - **Fault Isolation:** If one application pool fails, others continue operating.
> - **Configurable Settings:** Recycling, identity, pipeline mode (Classic or Integrated), and `.NET` runtime version.
> - **Identities:** `ApplicationPoolIdentity`, `NetworkService`, `LocalSystem`, or a custom account.

> [!question]- What is a Hyper-V Checkpoint?
> Formerly called a *Snapshot*, it captures the state of a VM (disk, memory, and VM state) at a specific point in time.
> - **Types:** Production Checkpoints (VSS-based) and Standard Checkpoints.
> - **PowerShell:** `Checkpoint-VM`, `Restore-VMCheckpoint`.
> > [!warning] Excessive checkpoints negatively impact performance and should be avoided in production environments.

> [!question]- How does Server Manager work?
> The centralized management console for Windows Server. Enables role/feature installation, remote server management, Server Groups, and monitoring. Includes the **Best Practices Analyzer (BPA)** and integrates Event Viewer, Services, and Performance tools.

> [!question]- What is Windows Admin Center (WAC)?
> A browser-based server management platform supporting [[Hyper-V]], Failover Clusters, and Azure hybrid management.
> - **Communication:** Over HTTPS (port `443`) using PowerShell and WMI in the background.
> - **Features:** Remote Desktop, Event Viewer, Firewall, and Certificate management.
> - **Modes:** Gateway mode or Desktop mode.

> [!question]- What is the difference between Disk Management and Storage Spaces?
> | Feature | Disk Management (`diskmgmt.msc`) | Storage Spaces |
> | :--- | :--- | :--- |
> | **Scope** | Basic disk ops (partitioning, formatting, drive letters) | Software-defined storage (pools, tiering, thin provisioning) |
> | **Disks / Resiliency** | MBR & GPT; Basic & Dynamic disks | Mirror & Parity resiliency; more flexible and scalable |
> | **Hyper-Converged** | N/A | **Storage Spaces Direct (S2D)** enables HCI |

> [!question]- What is File Server Resource Manager (FSRM)?
> A toolset for managing file servers: **quota management**, **file screening** (blocking specific file types), **storage reporting**, and **file classification**.
> ```powershell
> Install-WindowsFeature FS-Resource-Manager
> ```

> [!question]- What is iSCSI and how is it configured?
> **Internet Small Computer Systems Interface** allows SCSI-based storage access over IP networks.
> - **Components:** **iSCSI Target** (storage server hosting virtual disks) and **iSCSI Initiator** (client).
> - **Enhancements:** **MPIO** for redundancy/performance; **CHAP** for authentication.
> ```powershell
> Install-WindowsFeature FS-iSCSITarget-Server
> ```

> [!question]- How is a Print Server configured?
> Centralizes printer management via the **Print Management** console (drivers on server, centralized queues, deployed via Group Policy Preferences). Supports **Printer Pooling** (multiple printers sharing one queue).
> ```powershell
> Install-WindowsFeature Print-Server
> ```

> [!question]- What is Windows Deployment Services (WDS)?
> Enables OS deployment over the network using **PXE boot** (requires [[DHCP]]).
> - **Images:** Uses Boot Images, Install Images, and Capture Images (from reference systems).
> - **Multicast:** Deploys to multiple machines simultaneously.
> - **PowerShell:** `Add-WdsBootImage`, `Add-WdsInstallImage`.

> [!question]- What is Data Deduplication?
> Reduces disk usage by eliminating duplicate data blocks (32–128 KB chunks) as a background process on **NTFS** volumes (common for file servers, VDI, and backups).
> ```powershell
> Enable-DedupVolume -Volume D:\
> Get-DedupStatus
> ```

> [!question]- How is Server Core used as a Hyper-V host?
> Ideal for [[Hyper-V]] due to minimal overhead and reduced attack surface. Managed remotely via Hyper-V Manager, PowerShell (`New-VM`, `Set-VM`, `Start-VM`), or **SCVMM**.
> ```powershell
> Install-WindowsFeature Hyper-V -IncludeManagementTools
> ```

> [!question]- What is Failover Cluster Quorum?
> Determines whether a cluster remains operational using a voting mechanism to prevent **split-brain** scenarios (often using a disk or file share **witness**).
> - **Models:** Node Majority, Node and Disk Majority, Node and File Share Majority.
> ```powershell
> Get-ClusterQuorum
> ```

> [!question]- What are Cluster Shared Volumes (CSV)?
> Allows multiple cluster nodes to access the same disk simultaneously (commonly used for Hyper-V and SQL Server).
> - **Path:** `C:\ClusterStorage\`
> - **Modes & Performance:** Supports Direct I/O and Redirected I/O; **CSV cache** improves read performance.

> [!question]- What are Windows Server Containers?
> Provide application isolation using container technology (typically with [[Docker]], Windows Server Core base images, and Kubernetes Windows nodes).
> - **Windows Server Containers:** Shared kernel.
> - **Hyper-V Containers:** Isolated kernel.

> [!question]- What are the key features of SMB 3.0?
> - Transparent failover & continuously available file shares
> - Scale-out file shares
> - **SMB Direct (RDMA):** High-performance transfers
> - **SMB Multichannel:** Uses multiple network interfaces
> - **SMB Encryption:** Uses `AES-128`

> [!question]- What is BranchCache?
> Reduces WAN bandwidth usage in branch offices for **HTTP** and **SMB** content (configured via PowerShell or Group Policy).
> - **Modes:** Hosted Cache (central server) and Distributed Cache (peer-to-peer).

> [!question]- What are Work Folders?
> On-premises file synchronization across devices (alternative to cloud sync). Requires **SSL certificates**; files are encrypted at rest and in transit.
> ```powershell
> Install-WindowsFeature FS-SyncShareService
> ```

> [!question]- What is DirectAccess?
> An always-on remote access solution (alternative to traditional VPNs) using **IPsec** and **IPv6 tunneling**. Requires domain-joined clients, a **Network Location Server (NLS)** to detect internal vs. external connectivity, and **Public PKI certificates**.

> [!question]- What is a Windows Server update strategy?
> - Use **WSUS** or **SCCM** with defined maintenance windows.
> - Test updates on **pilot groups** first.
> - Avoid automatic updates on **Domain Controllers** and critical servers.
> - Use **Cluster-Aware Updating (CAU)** for rolling updates on cluster nodes.

---

## Section 11 – Windows Security & Permissions

> [!question]- What are NTFS permissions?
> - Control access to files and folders (both **local** and **network** access).
> - **Basic Permissions:** Full Control, Modify, Read & Execute, Read, Write (plus granular Advanced permissions).
> - **Rules:** Child objects inherit parent permissions; effective permissions combine all group memberships; **Explicit Deny** overrides **Allow**.
> - **PowerShell:** `Get-Acl`, `Set-Acl`.

> [!question]- What is the difference between Share permissions and NTFS permissions?
> - **Share Permissions:** Apply **only to network access** (`Read`, `Change`, `Full Control`).
> - **NTFS Permissions:** Apply to both **local and network** access.
> - **Effective Permission:** The **most restrictive** combination of Share and NTFS permissions.
> - **Best Practice:** Grant `Everyone: Full Control` at the Share level and enforce granular access using **NTFS permissions**.
> - **Access-Based Enumeration (ABE):** Ensures users only see folders they have permission to access.

> [!question]- What is BitLocker?
> - Disk encryption technology protecting **data at rest** on OS drives, data drives, and removable media (**BitLocker To Go**).
> - **Algorithms:** `AES-128` and `AES-256`.
> - **Unlock Methods:** TPM, PIN, or USB key; recovery keys should be backed up to **Active Directory** or secure storage.
> - **PowerShell:** `Enable-BitLocker`

> [!question]- How is Windows Defender Antivirus managed?
> - Built-in anti-malware solution with real-time protection, cloud-delivered protection, automatic sample submission, and Quick/Full/Custom scans.
> - **Centralized Management:** Via **Group Policy**.
> - **PowerShell:** `Update-MpSignature` (update signatures), `Add-MpPreference` (configure exclusions).

> [!question]- What is AppLocker?
> - Application control (**whitelisting**) technology configured via **Group Policy**.
> - Controls Executables, Scripts, Windows Installer files, and Packaged Apps based on **Publisher**, **Path**, or **File Hash**.
> - Default rules allow administrators full access; always test in **Audit mode** before enforcement.

> [!question]- What is User Account Control (UAC)?
> - Prompts for approval when administrative privileges are required (using elevation prompts and **Administrator Approval Mode**), reducing unauthorized system changes.
> - Levels range from *Always Notify* to *Never Notify*; managed via **Group Policy** or registry settings.

> [!question]- What is Credential Guard?
> - Protects credentials using **virtualization-based security (VBS)** by isolating the **LSA** process to mitigate **pass-the-hash** and credential theft attacks.
> - Supported on Windows 10 Enterprise and Windows Server 2016+; requires **UEFI**, **TPM**, and **Hyper-V**.

> [!question]- What is LAPS (Local Administrator Password Solution)?
> - Automatically manages and rotates unique local administrator passwords on domain-joined computers, storing them securely in **Active Directory**.
> - Reduces **lateral movement** and credential reuse attacks.
> - **PowerShell:** `Get-AdmPwdPassword`

> [!question]- What is Windows Hello for Business?
> - Enables **passwordless authentication** using biometrics (fingerprint, facial recognition) or PINs backed by **TPM-protected public/private key pairs**.
> - Integrates with on-premises **Active Directory** and **Azure AD** to replace traditional passwords.

> [!question]- What is a Certificate Authority (CA)?
> - Issues and manages digital certificates as part of a **[[PKI]]** (structured as **Root CAs** and **Subordinate CAs**).
> - **Certificate Templates** define usage and permissions; **Auto-enrollment** is configured via Group Policy.
> - **Revocation Checking:** Handled via **CRL** and **OCSP**.

> [!question]- How does Smart Card authentication work?
> - Provides **MFA** using certificates stored on physical cards (requires a smart card reader and **AD Certificate Services**).
> - Can be enforced via **Group Policy**; removing the card can automatically lock the session.

> [!question]- What is Windows Information Protection (WIP)?
> - A **Data Loss Prevention (DLP)** solution that protects corporate data from accidental leakage by separating personal and corporate data.
> - Supports automatic data classification/protection and integrates with **Intune** and **Azure Rights Management**.

> [!question]- What is a Security Baseline?
> - A predefined set of recommended security settings used to standardize and harden systems.
> - **Sources/Frameworks:** Microsoft **Security Compliance Toolkit**, **CIS Benchmarks**, and **STIGs**.

> [!question]- How is Audit Policy configured?
> - Records security-relevant events in the **Security log** (Advanced Audit Policy categories include Logon/Logoff, Object Access, Account Management).
> - Configured via **Group Policy** or `auditpol`; logs can be forwarded to **SIEM** systems.

> [!question]- How do you protect against ransomware?
> - Core defenses: **Offline and immutable backups**, anti-malware, application control, **least privilege**, and patch management.
> - Additional controls: Email filtering, user awareness training, network segmentation, and **Windows Defender Controlled Folder Access**.

> [!question]- What is Windows Defender Application Control (WDAC)?
> - Enterprise-grade application control enforcing execution policies using **code integrity rules** (supports kernel-mode and user-mode enforcement, plus Audit and Enforcement modes).
> - Integrates with **Hypervisor-protected Code Integrity (HVCI)**.

> [!question]- What is Multi-Factor Authentication (MFA)?
> - Requires **two or more** authentication factors to significantly reduce credential-based attacks:
>   - Something you **know** (password)
>   - Something you **have** (token or phone)
>   - Something you **are** (biometrics)

> [!question]- What is the Principle of Least Privilege?
> - Ensures users only have the minimum permissions necessary to perform their tasks, reducing the attack surface and limiting damage from compromised accounts.
> - Supported by **Just-In-Time (JIT)** access and **tiered administration** models.

> [!question]- What is Encryption at Rest vs Encryption in Transit?
> - **Encryption at Rest:** Protects stored data on disks (e.g., **[[BitLocker]]**).
> - **Encryption in Transit:** Protects data traveling over networks (e.g., **[[TLS]]**, **[[IPsec]]**).
> - Both require effective **key management** and support regulatory compliance.


---

## Section 12 – Backup, Recovery, and Monitoring

> [!question]- What is VSS (Volume Shadow Copy Service)?
> - Creates point-in-time snapshots of volumes so backup applications can consistently back up open/active files (also powers the **"Previous Versions"** feature).
> - **Components:** **Providers** (system, software, hardware) and **Writers** (e.g., SQL Server, Exchange, Active Directory).
> - **Commands:** `vssadmin list shadows`, `vssadmin create shadow`.



---

> [!question]- What are the main DNS Record types?
> - **A:** Maps a hostname to an **IPv4** address.
> - **AAAA:** Maps a hostname to an **IPv6** address.
> - **CNAME:** Creates an alias pointing to another hostname.
> - **MX:** Specifies mail servers and their priority.
> - **PTR:** Reverse DNS lookup (IP → hostname).
> - **TXT:** Stores text data such as **SPF** or **DKIM**.
> - **SRV:** Defines service locations (used heavily by **[[Active Directory]]**).

> [!question]- What is the difference between DNS Forwarders and Root Hints?
> - **DNS Forwarders:** Send unresolved queries to another designated DNS server (preferred in internal enterprise environments).
> - **Conditional Forwarders:** Forward queries for specific domain names.
> - **Root Hints:** Provide a built-in list of internet root DNS servers used to resolve external domains directly.

> [!question]- How does a Routing Table work?
> - Stores network routes used to forward packets, listing **destination**, **netmask**, **gateway**, **interface**, and **metric**.
> - **Longest Prefix Match** determines route selection.
> - Routes can be added manually (**static**) or learned via dynamic protocols (**RIP**, **OSPF**).

> [!question]- What is Port Forwarding?
> - Redirects traffic from a public IP and port to a private internal IP and port (e.g., `Public_IP:8080` → `192.168.1.10:80`).
> - Configured on **NAT routers** or **firewalls**; a **DMZ** is often used for publicly accessible servers.
> - **Alternatives / Automation:** **UPnP** enables automatic port forwarding; **Reverse Proxies** serve as a more secure alternative.

> [!question]- What are NetBIOS and WINS?
> - **NetBIOS:** Legacy Windows naming and communication system (NetBIOS over TCP/IP uses ports `137–139`).
> - **WINS:** Resolves NetBIOS names to IP addresses (**deprecated** in favor of **[[DNS]]**).
> - **Command:** `nbtstat -a <hostname>`

> [!question]- What is NIC Teaming (LBFO)?
> - Combines multiple network interfaces for **redundancy** and **load balancing**.
> - **Modes:** Switch Independent and Switch Dependent.
> - **Load Balancing Algorithms:** Address Hash, Hyper-V Port, and Dynamic.
> - Supports configuring a **standby adapter** for failover.
> - **PowerShell:** `New-NetLbfoTeam`

> [!question]- What is SMB Signing?
> - Digitally signs [[SMB]] packets to prevent tampering and **man-in-the-middle (MITM)** attacks.
> - Required by default on **Domain Controllers**; has a minor performance impact.
> - Configured via **PowerShell** or **Group Policy**.

