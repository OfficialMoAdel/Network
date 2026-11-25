---
title: "02 - Remote Access, VTP & Port Security"
date: 2026-11-25
tags:
  - cisco/ccna
  - networking/telnet
  - networking/ssh
  - networking/remote-access
  - networking/vtp
  - networking/port-security
  - networking/layer2-security
aliases:
  - CCNA Part 02
  - Remote Access VTP and Port Security
status: completed
related:
  - "[[00 - Cisco CCNA MOC]]"
  - "[[01 - Fundamentals, VLANs & Inter-VLAN]]"
  - "[[03 - STP, EtherChannel & IPv4 Routing]]"
  - "[[CCNA Commands & Troubleshooting]]"
---

# 02 - Remote Access, VTP & Port Security

> [!abstract] Navigation
> **Previous:** [[01 - Fundamentals, VLANs & Inter-VLAN]] | **Index:** [[00 - Cisco CCNA MOC]] | **Next:** [[03 - STP, EtherChannel & IPv4 Routing]] | **Commands:** [[CCNA Commands & Troubleshooting#11) Switch Security & Port Security|Port Security CLI]]
> **Covers:** PDF Pages 25–43 (Telnet Plaintext Risks, Router & Switch SSH, Switch Management IP & Default Gateway, VTP Modes & Revision Reset, and Switch Port Security: Static MAC, Sticky MAC, & Violation Modes).

---

## 1. Remote Access with Telnet

**Telnet** provides remote CLI access over **TCP Port 23** via Virtual Teletype (`vty`) lines (up to 16 concurrent sessions: `line vty 0 15`).

```cisco
! Basic Telnet (line password only)
Router(config)# enable secret cisco
Router(config)# line vty 0 2
Router(config-line)# password telnet123
Router(config-line)# login
Router(config-line)# transport input telnet
Router(config-line)# exit

! Local Username & Password Authentication
Router(config)# username user1 secret password1
Router(config)# line vty 0 2
Router(config-line)# login local
Router(config-line)# exit
```

> [!info] VTY `transport input` Options
> - `transport input telnet` — Allows **Telnet only**.
> - `transport input ssh` — Allows **SSH only** (recommended).
> - `transport input all` — Allows **both Telnet and SSH**.
> - `enable secret <password>` is **required** to enter Privileged EXEC mode (`#`) remotely.

### Why Telnet Is Insecure (Plaintext Sniffing)

```mermaid
sequenceDiagram
    participant Admin as Admin PC (TCP Src Port: 1026)
    participant Sniffer as Attacker / Packet Capture
    participant Router as Cisco Router (TCP Dst Port: 23)

    Admin->>Router: Telnet Session (Plaintext TCP Port 23)
    Note over Admin,Router: TELNET DATA: "show run" (readable in cleartext!)
    Admin-->>Sniffer: Intercepted PDU exposes usernames, passwords & CLI commands
```

> [!danger] Security Warning
> ==🔴Never use Telnet in production environments.== Always restrict VTY lines to `transport input ssh` so all management traffic is encrypted.

---

## 2. Remote Access with SSH

**SSH (Secure Shell)** encrypts remote management traffic over **TCP Port 22** using RSA keys.

### SSH Prerequisites on Cisco IOS

1. Configure a **Hostname** (cannot remain default `Router` or `Switch`).
2. Configure an **IP Domain Name** (`ip domain-name`).
3. Create a local **Username & Secret** (`username ... secret ...`) and **`enable secret`**.
4. Generate **RSA Crypto Keys** (`crypto key generate rsa` with at least **1024 bits**).
5. Enable `login local` and `transport input ssh` on `line vty`.

### Configuring SSH on a Router

```cisco
Router(config)# hostname Core-Router
Core-Router(config)# enable secret cisco
Core-Router(config)# username user1 secret password1
Core-Router(config)# ip domain-name agunacourse.com

Core-Router(config)# crypto key generate rsa
! How many bits in the modulus [512]: 1024

Core-Router(config)# line vty 0 1
Core-Router(config-line)# login local
Core-Router(config-line)# transport input ssh
Core-Router(config-line)# exit
```

### Switch Management IP, Default Gateway & SSH

A Layer 2 switch needs a management **SVI** (`interface vlan 10`) and an **`ip default-gateway`** to be managed from other VLANs or subnets.

```mermaid
flowchart TB
    R["Core-Router<br/>VLAN 10 Gateway: 192.168.10.1/24"]

    SW1["SW1 (Switch 1)<br/>Vlan10: 192.168.10.30/24<br/>Default GW: 192.168.10.1<br/>SSH Enabled"]
    SW2["SW2 (Switch 2)<br/>Vlan10: 192.168.10.31/24<br/>Default GW: 192.168.10.1"]

    PC0["PC0 (VLAN 10)<br/>SSH Client -> 192.168.10.30"]
    PC2["PC2 (Other VLAN)<br/>Inter-VLAN Ping -> .30 & .31"]

    R ===|"Trunk"| SW1
    SW1 ===|"Trunk"| SW2
    SW1 --- PC0
    SW2 --- PC2
```

```cisco
! --- Full Switch SSH Configuration (SW1) ---
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.30 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway 192.168.10.1

Switch(config)# hostname SW1
SW1(config)# enable secret cisco
SW1(config)# username user1 secret password1
SW1(config)# ip domain-name agunacourse.com
SW1(config)# crypto key generate rsa
! How many bits in the modulus [512]: 1024

SW1(config)# line vty 0 4
SW1(config-line)# login local
SW1(config-line)# transport input ssh
SW1(config-line)# exit

! --- SW2 Management IP ---
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.31 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway 192.168.10.1
```

> [!tip] Initial Ping Packet Loss (ARP Resolution)
> Seeing 1 timed-out packet (`80%` success rate) on the first ping is normal while switches and hosts resolve MAC addresses via [[01 - Fundamentals, VLANs & Inter-VLAN#5. ARP Table (PC) vs. MAC Address Table (Switch)|ARP]].

---

## 3. VLAN Trunking Protocol (VTP)

**VTP** automatically propagates VLAN database changes across trunked switches belonging to the same **VTP Domain**.

```mermaid
flowchart LR
    SW1["SW1 (2960)<br/>VTP Server"] ===|"Fa0/1 — Trunk — Fa0/1"| SW2["SW2 (2960)<br/>VTP Transparent"]
    SW2 ===|"Fa0/2 — Trunk — Fa0/1"| SW3["SW3 (2960)<br/>VTP Client"]
    SW3 ===|"Fa0/2 — Trunk — Fa0/1"| SW4["SW4 (2960)<br/>VTP Server"]
```

| VTP Mode | Create / Modify / Delete VLANs | Propagates Own Changes | Syncs Database from VTP | Forwards VTP Advertisements |
| :--- | :---: | :---: | :---: | :---: |
| **Server** | Yes | Yes | Yes | Yes |
| **Transparent** | Yes (local only) | No | No | Yes (passes through) |
| **Client** | No | No | Yes | Yes |

^vtp-modes

```cisco
! All switches must share the exact same Domain Name & Password
Switch(config)# vtp mode server        ! Options: server | transparent | client
Switch(config)# vtp domain AGUNACOURSE
Switch(config)# vtp password 123
```

### Solving the VTP Revision Number Problem

> [!danger] The VTP Overwrite Hazard
> VTP trusts the **highest `Configuration Revision` number**. Plugging in a used switch (even in **Client** mode) with a higher revision (e.g., `14`) ==🔴will overwrite the entire production VLAN database==.

```mermaid
flowchart LR
    A["Check Status<br/>do show vtp status<br/>(Revision: 14)"] --> B["Set Transparent Mode<br/>vtp mode transparent"]
    B --> C["Verify Reset<br/>do show vtp status<br/>(Revision: 0)"]
    C --> D["Set Target Mode<br/>vtp mode client / server<br/>& Connect to Trunk"]
```

```cisco
Switch(config)# do show vtp status       ! Revision: 14
Switch(config)# vtp mode transparent     ! Resets Revision to 0
Switch(config)# do show vtp status       ! Revision: 0
```

### VTP + Router-on-a-Stick Lab Topology

```mermaid
flowchart TB
    R1["R1 (Cisco 2811)<br/>Fa0/0.10 – Fa0/0.80<br/>Inter-VLAN Routing + DHCP"]
    SRV["Core Switch (2960-24TT)<br/>VTP Server"]

    C1["SW-Client 1 (2960)"]
    C2["SW-Client 2 (2960)"]
    C3["SW-Client 3 (2960)"]

    V_L["VLAN 40, 50, 60<br/>PC1–PC4, Laptop1–2"]
    V_M["VLAN 10, 20, 30<br/>PC9–PC11, Laptop3–6, Laptop9–13"]
    V_R["VLAN 70, 80<br/>PC7–PC8, Laptop7–8"]

    R1 ===|"Fa0/0 <== Trunk ==> Fa0/1"| SRV
    SRV ===|"Fa0/2 — Trunk — Fa0/1"| C1
    SRV ===|"Fa0/3 — Trunk — Fa0/1"| C2
    SRV ===|"Fa0/4 — Trunk — Fa0/1"| C3

    C1 --- V_L
    C1 --- V_M
    C2 --- V_M
    C3 --- V_M
    C3 --- V_R
```

---

## 4. Switch Port Security

**Port Security** restricts access on a switch access port by limiting allowed **MAC addresses** and defining an action when an unauthorized device connects.

```mermaid
flowchart LR
    SVR1["SVR1 (Server)<br/>MAC: 00E0.B09A.213A<br/>Static MAC + Shutdown"]
    SVR2["SVR2 (Server)<br/>MAC: 00E0.8F10.23C1<br/>Sticky MAC + Shutdown"]
    SVR3["SVR3 (Server)<br/>MAC: 0001.42E9.619C<br/>Sticky MAC + Protect"]
    SVR4["SVR4 (Server)<br/>Sticky MAC + Restrict"]
    HACKER["Hacker PC<br/>MAC: 000A.4150.9C57<br/>(Unauthorized Device)"]

    SW0["Switch0 (2960-24TT)<br/>Port Security Enabled"]
    PC1["PC1<br/>Legitimate Client"]

    SVR1 ---|"Fa0/1"| SW0
    SVR2 ---|"Fa0/2"| SW0
    SVR3 ---|"Fa0/3"| SW0
    SVR4 ---|"Fa0/5"| SW0
    SW0 ---|"Fa0/4"| PC1
    HACKER -.->|"Attempts Cable Swap"| SW0

    style HACKER fill:#fee2e2,stroke:#dc2626,color:#000
```

> [!important] Prerequisite: Static Access Mode Required
> Enabling `switchport port-security` on a default `dynamic auto` port is rejected (`Command rejected: FastEthernet0/5 is a dynamic port.`). Always configure **`switchport mode access`** first.

### Static MAC & Shutdown Violation (`Fa0/1`)

```cisco
Switch(config)# interface fa0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security mac-address 00E0.B09A.213A
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation shutdown
```

#### Violation & Recovery Workflow

```mermaid
flowchart LR
    A["SVR1 Connected<br/>Fa0/1: Secure-up"] -->|"Hacker Swaps Cable<br/>& Sends Packet"| B["Violation Triggered<br/>Fa0/1: Secure-shutdown<br/>(err-disabled)"]
    B -->|"Reconnect SVR1 Cable"| C["Port Remains Down<br/>('no shutdown' alone fails)"]
    C -->|"Run: shutdown<br/>then: no shutdown"| D["Port Restored<br/>Fa0/1: Secure-up"]

    style B fill:#fee2e2,stroke:#dc2626,color:#000
    style D fill:#dcfce7,stroke:#16a34a,color:#000
```

```cisco
! Recovering an err-disabled (Secure-shutdown) port
Switch(config)# interface fa0/1
Switch(config-if)# shutdown
Switch(config-if)# no shutdown
Switch(config-if)# do show port-security interface fa0/1
```

---

### Sticky MAC Learning (`Fa0/2`) & Violation Modes Comparison

| Violation Mode | Drops Unauthorized Frames | Increments Violation Counter & Logs | Shuts Down Port (`err-disabled`) | Manual Recovery Needed |
| :--- | :---: | :---: | :---: | :---: |
| **`protect`** | Yes | **No** (stays `0`) | No (stays `Secure-up`) | No |
| **`restrict`** | Yes | **Yes** (logs & counts each violation) | No (stays `Secure-up`) | No |
| **`shutdown`** *(default)* | Yes | **Yes** | ==🔴Yes (`Secure-shutdown`)== | ==Yes (`shut` / `no shut`)== |

^violation-modes

```cisco
! Sticky MAC + Shutdown (Fa0/2)
Switch(config)# interface fa0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security mac-address sticky
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation shutdown

! Sticky MAC + Protect (Fa0/3 — silent drop, counter stays 0, port stays up)
Switch(config)# interface fa0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security mac-address sticky
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation protect

! Sticky MAC + Restrict (Fa0/5 — drops frames, increments counter, port stays up)
Switch(config)# interface fa0/5
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security mac-address sticky
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation restrict
```
