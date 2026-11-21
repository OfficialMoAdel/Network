---
title: CCNA Commands & Troubleshooting
aliases:
  - CCNA Cheat Sheet
  - CCNA Commands
  - Troubleshooting Show Commands
  - Show Commands by Symptom
  - Troubleshoot Cisco Networks Faster
  - 40 Essential Commands
tags:
  - ccna
  - networking
  - cheat-sheet
  - troubleshooting
  - show-commands
date: 2026-11-10
updated: 2026-11-14
status: complete
---

# CCNA Commands & Troubleshooting

> [!info] عن هذه الملاحظة
> مرجع موحّد (دمج ملف الأوامر Part 1 + Part 2 مع ملف استكشاف الأخطاء):
> ==Part A== الأوامر حسب الموضوع — ==Part B== استكشاف الأخطاء حسب الأعراض (40 أمرًا).
> النظرية واللابات: [[CCNA Theory & Labs]] — نظرية STP: [[STP (Spanning Tree Protocol)]]

## الفهرس

**Part A — الأوامر حسب الموضوع:**
- [[#1) Basic CLI & Device Management]]
- [[#2) Interfaces — Router & Switch]]
- [[#3) VLANs, Trunks & VTP]]
- [[#4) STP, EtherChannel & CDP]]
- [[#5) IPv4, Static Routing & RIPv2]]
- [[#6) OSPF]]
- [[#7) EIGRP]]
- [[#8) IPv6]]
- [[#9) ACLs — Complete Reference]]
- [[#10) DHCP, NAT & Management]]
- [[#11) Switch Security & Port Security]]
- [[#12) WAN — PPP & Frame Relay]]

**Part B — استكشاف الأخطاء حسب الأعراض:**
- [[#General Debug Tools]]
- [[#Start Here]]
- [[#Physical Links & Ports]]
- [[#VLAN & Trunking]]
- [[#STP & EtherChannel]]
- [[#IP Reachability & Routing]]
- [[#OSPF Troubleshooting]]
- [[#EIGRP Troubleshooting]]
- [[#BGP Troubleshooting]]
- [[#ACL & NAT Troubleshooting]]
- [[#DHCP & Network Services]]

**[[#Hands-On Practice]]**

---

## Part A — Commands by Topic

### 1) Basic CLI & Device Management
#ccna/cli #ccna/device-management

#### 1.1 Mode Navigation

```text
enable
disable
configure terminal
end
exit
do show running-config
```

#### 1.2 الإعداد الأساسي الكامل — Router

```text
Router(config)#hostname R1
R1(config)#enable secret cisco
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#logging synchronous
R1(config-line)#exec-timeout 30 0
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#logging synchronous
R1(config-line)#exec-timeout 30 0
R1(config-line)#exit
R1(config)#line aux 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#logging synchronous
R1(config-line)#exec-timeout 30 0
R1(config-line)#exit
R1(config)#banner motd $
-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
UNAUTHORIZED ACCESS IS PROHIBITED
-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
$
R1(config)#alias exec c configure terminal
R1(config)#alias exec s show ip interface brief
R1(config)#alias exec sr show running-config
R1(config)#no ip domain-lookup
R1(config)#service password-encryption
R1(config)#ip domain-name example.com
R1(config)#username admin password cisco
R1(config)#username admin secret Cisco123   (بديل أكثر أمانًا عن password)
R1(config)#crypto key generate rsa
How many bits in the modulus [512]: 1024
R1(config)#ip ssh version 2
R1(config)#line vty 0 4
R1(config-line)#login local
R1(config-line)#transport input telnet ssh   (telnet only / ssh only / both)
```

> [!note] Router vs Switch
> هذا القسم يتضمن أوامر IOS متطابقة تمامًا على الراوترات والسويتشات، باستثناء جزء `line aux 0` الذي يُضبط على الراوتر فقط لأن السويتشات لا تملك منفذ auxiliary.

#### 1.3 الفروق الخاصة بالسويتش

```text
SW1(config)#enable secret cisco        (MD5 hash)
SW1(config)#enable password notcisco   (clear text)
```

**Switch IP & Default Gateway (SVI):**

```text
SW1(config)#interface vlan 1
SW1(config-if)#ip address 172.16.1.11 255.255.255.0   (or dhcp)
SW1(config-if)#no shutdown
SW1(config)#ip default-gateway 172.16.1.1
```

**Working Environment:**

```text
SW1(config)#line vty 0 4
SW1(config-line)#history size 15
SW1(config-line)#exec-timeout 10 30
SW1(config-line)#logging synchronous     (also valid for line con 0)
```

> [!note] SSH on the Switch
> نفس خطوات الراوتر في 1.2: `ip domain-name` + `username` + `crypto key generate rsa` + `ip ssh version 2` + `login local` + `transport input`.
> نطاق الـ key modulus: ==360–2048==.

#### 1.4 الملفات والحفظ

```text
copy running-config startup-config    (press enter to confirm filename)
write memory                          (أو الاختصار: wr)
copy startup-config running-config
erase startup-config
reload
show running-config
show startup-config
```

#### 1.5 التحقق من الجهاز

| الأمر | الوصف |
| --- | --- |
| SW1#show version | Shows information about the switch and its interfaces, RAM, NVRAM, flash, IOS, etc. |
| SW1#show history | Lists the commands currently held in the history buffer |
| SW1#show crypto key mypubkey rsa | Shows the public encryption key used for SSH |
| SW1#show dhcp lease | Shows information about the leased IP address (when an interface gets its IP via DHCP) |

**التحقق من الواجهات والاستجابة الأولى:** [[#2) Interfaces — Router & Switch]] • [[#Start Here]]

### 2) Interfaces — Router & Switch
#ccna/interfaces #ccna/switching

#### 2.1 Router Interfaces

```text
R1(config)#interface fastEthernet 0/0
R1(config-if)#description LINK_TO_LOCAL_LAN_THROUGH_SW1
R1(config-if)#ip address 172.16.1.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface serial 0/1/0
R1(config-if)#description WAN_CONNECTION_TO_R2
R1(config-if)#ip address 10.1.1.1 255.255.255.252
R1(config-if)#clock rate 128000
R1(config-if)#no shutdown
```

> [!note] Clock rate
> يُضبط الـ clock rate على جهة الـ ==DCE== فقط (عادة جهة ISP). على راوترك الذي يعمل كـ DTE لا تحتاج لضبط clocking.

#### 2.2 Speed / Duplex & Range

```text
SW1(config)#interface fastEthernet 0/1
SW1(config-if)#description LINK TO INTERNET ROUTER
SW1(config-if)#speed 100        (options: 10, 100, auto)
SW1(config-if)#mdix auto
SW1(config)#interface range fastEthernet 0/5 - 10
SW1(config-if-range)#duplex full   (options: half, full, auto)
```

> [!tip] الكلمة المفتاحية `range`
> تُستخدم لضبط مجموعة منافذ دفعة واحدة.

#### 2.3 Router-on-a-Stick (Inter-VLAN Routing)

```text
R1(config)#interface fastEthernet 0/0
R1(config-if)#no shutdown
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip address 192.168.10.1 255.255.255.0
R1(config-subif)#interface fastEthernet 0/0.20
R1(config-subif)#encapsulation dot1q 20
R1(config-subif)#ip address 192.168.20.1 255.255.255.0
```

#### 2.4 التحقق من الواجهات

```text
SW1#show mac address-table
SW1#clear mac address-table dynamic
SW1#show interface vlan 1   (status, protocol, duplex, speed, encapsulation, last 5 min traffic)
```

**فحص الحالة والأخطاء والوصف:** [[#Physical Links & Ports]] • [[#Start Here]]

### 3) VLANs, Trunks & VTP
#ccna/vlans #ccna/trunks #ccna/vtp

#### 3.1 VLAN Creation

```text
vlan 10
name SALES
vlan 20
name VOICE
```

#### 3.2 Access & Voice Ports

```text
SW1(config)#interface fastEthernet 0/5
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 10   (data)
SW1(config-if)#switchport voice vlan 12    (VoIP)
```

#### 3.3 Trunks (الخيارات الكاملة)

```text
SW1(config)#interface fastEthernet 0/1
SW1(config-if)#switchport mode trunk   (options: access, trunk, dynamic auto, dynamic desirable)
SW1(config-if)#switchport trunk native vlan 99
SW1(config-if)#switchport trunk allowed vlan 10,20,99
SW1(config-if)#switchport trunk allowed vlan add 10   (options: add, remove, all, except)
```

#### 3.4 Securing VLANs & Trunking

```text
SW1(config-if)#shutdown                (administratively disable unused interfaces)
SW1(config-if)#nonegotiate             (prevent trunking by disabling auto negotiation)
SW1(config-if)#switchport mode access  (or hardcode the port as an access port)
SW1(config-if)#switchport access vlan 222   (assign the port to an unused VLAN)
```

#### 3.5 VTP Configuration

```text
SW1(config)#vtp mode server      (options: server, client, transparent)
SW1(config)#vtp domain EXAMPLE   (case-sensitive)
SW1(config)#vtp password cisco   (optional, case-sensitive)
SW1(config)#vtp pruning          (optional, only works on VTP servers)
SW1(config)#vtp version 2        (optional)
```

ثم قم برفع الـ trunks بين السويتشات.

> [!note] وضع transparent
> يُستخدم عندما يريد المهندس تعطيل VTP على سويتش معيّن فقط.

#### 3.6 التحقق من VLANs وVTP

```text
SW1#show vlan {brief | id | name | summary}
SW1#show vtp status
SW1#show vtp password
```

**استكشاف الأخطاء حسب الأعراض:** [[#VLAN & Trunking]] — النظرية: [[CCNA Theory & Labs#3) VLANs — ملخص نظري]]

### 4) STP, EtherChannel & CDP
#ccna/stp #ccna/etherchannel #ccna/cdp

#### 4.1 STP Configuration

```text
SW1(config)#spanning-tree mode rapid-pvst          (options: mst, pvst, rapid-pvst)
SW1(config)#spanning-tree vlan 1 root primary
SW1(config)#spanning-tree vlan 1 root secondary
SW1(config)#spanning-tree [vlan 1] priority 8192   (priority must be a multiple of 4096)
SW1(config-if)#spanning-tree [vlan 1] cost 25
```

#### 4.2 PortFast & Protection

```text
SW1(config-if)#spanning-tree portfast
SW1(config-if)#spanning-tree bpduguard enable      (only on interfaces connected to end user hosts)
SW1(config-if)#spanning-tree guard root
SW1(config-if)#spanning-tree guard loop
```

#### 4.3 EtherChannel

```text
SW1(config-if)#channel-group 1 mode active   (options: active, auto, desirable, on)
SW1(config)#interface port-channel 1
SW1(config-if)#switchport mode trunk
```

#### 4.4 CDP & LLDP

```text
SW1(config)#cdp run            (enabling CDP globally)
SW1(config-if)#no cdp enable   (disabling CDP on a given interface)
SW1(config)#lldp run
```

#### 4.5 التحقق

```text
SW1#show spanning-tree [vlan 1] bridge   (Shows info about the local switch)
SW1#show spanning-tree interface g0/1 detail
SW1#show etherchannel 1                  (Shows the state of the etherchannels)
SW1#show interfaces port-channel 1
SW1#show lldp neighbors
```

| الأمر | الوصف |
| --- | --- |
| SW1#show cdp | Shows global information about CDP itself |
| SW1#show cdp interface fa0/2 | Shows information about CDP on a specific interface |
| SW1#show cdp neighbors | Shows information about the directly connected Cisco devices including interfaces names and capabilities |
| SW1#show cdp entry * | Same as show cdp neighbors detail |
| SW1#show cdp entry Sw2 | Shows detailed information about the specified entry only |

> [!note] نظرية STP
> النظرية الكاملة وأوامر التحقق الإضافية: [[STP (Spanning Tree Protocol)]]

**استكشاف الأخطاء حسب الأعراض:** [[#STP & EtherChannel]]

### 5) IPv4, Static Routing & RIPv2
#ccna/ipv4 #ccna/static-routing #ccna/rip

ضبط IP على الواجهات: [[#2) Interfaces — Router & Switch]]

#### 5.1 Static & Default Routes

```text
R1(config)#ip route 10.1.2.0 255.255.255.0 10.1.128.1   (using next hop)
R1(config)#ip route 10.1.2.0 255.255.255.0 Serial 0/0   (using exit interface)
R1(config)#ip route 0.0.0.0 0.0.0.0 199.1.1.1           (default route)
```

> [!note] Exit interface
> يمكن استخدام الـ exit interface فقط في وصلات الـ point-to-point serial.

#### 5.2 RIPv2 Configuration

```text
R1(config)#router rip
R1(config-router)#version 2
R1(config-router)#network 10.0.0.0   (written as an original class A)
R1(config-router)#no autosummary
R1(config-router)#passive-interface serial 0/0
```

#### 5.3 التحقق

```text
R1#show ip route rip   (Shows routes learned via RIP only)
```

**باقي أوامر التحقق (route / protocols / ping / traceroute):** [[#IP Reachability & Routing]]

### 6) OSPF
#ccna/ospf

#### 6.1 Configuration

Enter OSPF router configuration mode:

```text
R1(config)#router ospf 10   (process ID)
```

Configure one or more network commands to identify which interfaces will run OSPF:

```text
R1(config-router)#network 10.0.0.0 0.255.255.255 area 0
R1(config-router)#network 172.16.8.0 0.0.7.255 area 0
R1(config-router)#network 192.168.1.254 0.0.0.0 area 1
```

Configure router ID either by: (Optional)
- Using router-id ospf subcommand:

```text
R1(config-router)#router-id 1.1.1.1
```

- Configuring an IP address on a loopback interface:

```text
R1(config)#interface loopback 0
R1(config-if)#ip address 1.1.1.1 255.255.255.255
```

Change Hello and Dead intervals per interface: (Optional)

```text
R1(config-if)#ip ospf hello-interval 2
R1(config-if)#ip ospf dead-interval 6
```

Impact routing choices by tuning interface cost using one of the following ways: (Optional)

```text
R1(config-if)#ip ospf cost 55                          (changing interface cost)
R1(config-if)#bandwidth 128                            (Kbps — changing interface bandwidth)
R1(config-router)#auto-cost reference-bandwidth 1000   (Mbps — reference bandwidth used to calculate cost)
```

Disabling OSPF on a certain interface: (Optional)

```text
R1(config-router)#passive-interface serial 0/0
```

Advertise a default route into OSPF: (Optional)

```text
R1(config-router)#default-information originate
```

Configure maximum equal-cost paths: (Optional)

```text
R1(config-router)#maximum paths 6
```

#### 6.2 OSPF Authentication

```text
Type 0 authentication (none):
R1(config-if)#ip ospf authentication null

Type 1 authentication (clear text):
R1(config-if)#ip ospf authentication
R1(config-if)#ip ospf authentication-key cisco

Type 2 authentication (md5):
R1(config-if)#ip ospf authentication message-digest
R1(config-if)#ip ospf message-digest-key 1 md5 cisco
```

#### 6.3 التحقق

جميع أوامر التحقق من OSPF مع الشرح حسب الأعراض: [[#OSPF Troubleshooting]]
النظرية (أنواع LSA): [[CCNA Theory & Labs#4) OSPF LSA Types]]

### 7) EIGRP
#ccna/eigrp

#### 7.1 Configuration

Enter EIGRP configuration mode and define AS number:

```text
R1(config)#router eigrp 121   (AS number)
```

Configure one or more network commands to enable EIGRP on the specified interfaces:

```text
R1(config-router)#network 10.0.0.0
R1(config-router)#network 172.16.0.0 0.0.3.255
R1(config-router)#network 192.168.1.1 0.0.0.0
R1(config-router)#network 0.0.0.0 255.255.255.255
```

Disable auto summarization / Disable EIGRP on a specific interface: (Optional)

```text
R1(config-router)#no autosummary
R1(config-router)#passive-interface serial 0/0
```

Configure load balancing parameters: (Optional)

```text
R1(config-router)#maximum-paths 6
R1(config-router)#variance 4
```

Change interface Hello and Hold timers: (Optional)

```text
R1(config-if)#ip hello-interval eigrp 121 3
R1(config-if)#ip hold-time eigrp 121 10
```

Impacting metric calculations by tuning BW and delay of the interface: (Optional)

```text
R1(config-if)#bandwidth 265   (kbps)
R1(config-if)#delay 120       (tens of microseconds)
```

#### 7.2 EIGRP Authentication

Create an authentication key chain as follows:
- Create a key chain and give it a name:

```text
R1(config)#key chain MY_KEYS
```

- Create one or more keys giving them numbers:

```text
R1(config-keychain)#key 1
```

- Define the key value:

```text
R1(config-keychain-key)#key-string 1stKEY
```

- Define the life time of the keys (optional):

```text
R1(config-keychain-key)#send-lifetime [start time] [end time]
R1(config-keychain-key)#accept-lifetime [start time] [end time]
```

Enable md5 authentication mode for EIGRP on the interface, and refer to the correct key chain:

```text
R1(config-if)#ip authentication mode eigrp 121 md5
R1(config-if)#ip authentication key-chain eigrp 121 MY_KEYS
```

> [!warning] Key chain notes
> قيمة الـ key-string والـ mode يجب أن تكون متطابقة على كلا الراوترين.
> خيارات الـ lifetime للـ keys تتطلب ضبط ساعة الراوترين بشكل صحيح — الأفضل استخدام ==NTP== وإلا قد تحدث مشاكل.

#### 7.3 التحقق

```text
R1#show ip eigrp traffic   (Lists statistics on numbers of EIGRP messages sent and received by the router)
```

**باقي أوامر التحقق:** [[#EIGRP Troubleshooting]]

### 8) IPv6
#ccna/ipv6

#### 8.1 Global

```text
ipv6 unicast-routing
```

#### 8.2 Interface IPv6

```text
interface g0/0
ipv6 address 2001:db8:1::1/64
ipv6 address fe80::1 link-local
no shutdown
```

#### 8.3 Static & Verify

```text
ipv6 route ::/0 2001:db8:1::2
show ipv6 interface brief
show ipv6 route
show ipv6 neighbors
ping 2001:db8:1::2
traceroute ipv6 2001:4860:4860::8888
```

### 9) ACLs — Complete Reference
#ccna/acl #ccna/security

#### 9.1 Standard ACL

Plan the location (router and interface) and direction (in or out) on that interface:
- Standard ACL should be placed as close as possible to the ==destination== of the packet.
- Identify the source IP addresses of packets as they go in the direction that the ACL is examining.

Use a remark to describe the ACL: (Optional)

```text
R1(config)#access-list 1 remark ACL TO DENY ACCESS FROM SALES VLAN
```

Create the ACL, keeping the following in mind:
- ACL uses ==first-match== logic.
- There is an implicit ==deny any== at the end of the ACL.

```text
R1(config)#access-list 2 deny 192.168.1.77
R1(config)#access-list 2 deny 192.168.1.64 0.0.0.31
R1(config)#access-list 2 permit 10.1.0.0 0.0.255.255
R1(config)#access-list 2 deny 10.0.0.0 0.255.255.255
R1(config)#access-list 2 permit any
```

Enable the ACL on the chosen router interface in the correct direction (in or out):

```text
R1(config-if)#ip access-group 2 out
```

> [!note] Standard ACL number ranges
> `1 – 99` و `1300 – 1999`

#### 9.2 Standard ACL to Limit Telnet / SSH Access

Create the ACL that defines the permitted telnet clients, then apply it inbound the vty lines:

```text
R1(config)#access-list 99 remark ALLOWED TELNET CLIENTS
R1(config)#access-list 99 permit 192.168.1.128 0.0.0.15
R1(config)#line vty 0 4
R1(config-line)#access-class 99 in
```

#### 9.3 Extended ACL

Note:
- Extended ACL should be placed as close as possible to the ==source== of the packet.
- Extended ACL matches packets based on source & des. IP addresses, protocol, source & des. port numbers and other criteria as well.

```text
R1(config)#access-list 101 remark MY_ACCESS_LIST
R1(config)#access-list 101 deny ip host 10.1.1.1 host 10.2.2.2
R1(config)#access-list 101 deny tcp 10.1.1.0 0.0.0.255 any eq 23
R1(config)#access-list 101 deny icmp 10.1.1.1 0.0.0.0 any
R1(config)#access-list 101 deny tcp host 10.1.1.0 host 10.0.0.1 eq 80
R1(config)#access-list 101 deny udp host 10.1.1.7 eq 53 any
R1(config)#access-list 101 permit ip any any
R1(config)#interface fastEthernet 0/0
R1(config-if)#ip access-group 101 in
```

> [!note] Extended ACL number ranges
> `100 – 199` و `2000 – 2699`

#### 9.4 Named ACLs & Sequence Editing

Note:
- Named ACLs use names to identify ACLs rather than numbers, and commands that permit or deny traffic are written in a sub mode called named ACL mode (nacl).
- Named ACL enables the editing of the ACL (deleting or inserting statements) by sequencing statements of the ACL.

Named standard ACL:

```text
R1(config)#ip access-list standard MY_STANDARD_ACL
R1(config-std-nacl)#permit 10.1.1.0 0.0.0.255
R1(config-std-nacl)#deny 10.2.2.2
R1(config-std-nacl)#permit any
R1(config)#interface fastEthernet 0/1
R1(config-if)#ip access-group MY_STANDARD_ACL out
```

Named extended ACL:

```text
R1(config)#ip access-list extended MY_EXTENDED_ACL
R1(config-ext-nacl)#deny icmp 10.1.1.1 0.0.0.0 any
R1(config-ext-nacl)#deny tcp host 10.1.1.0 host 10.0.0.1 eq 80
R1(config-ext-nacl)#permit ip any any
R1(config)#interface fastEthernet 0/1
R1(config-if)#ip access-group MY_EXTENDED_ACL in
```

مثال عملي — السماح للويب فقط (Web Only):

```text
R1(config)#ip access-list extended WEB_ONLY
R1(config-ext-nacl)#permit tcp 192.168.10.0 0.0.0.255 any eq 80
R1(config-ext-nacl)#permit tcp 192.168.10.0 0.0.0.255 any eq 443
R1(config-ext-nacl)#deny ip any any
R1(config)#interface g0/0
R1(config-if)#ip access-group WEB_ONLY out
```

Editing ACL using sequence numbers:

```text
R1(config)#ip access-list extended MY_EXTENDED_ACL
R1(config-ext-nacl)#no 20   (deletes the statement of sequence number 20)
R1(config)#ip access-list standard 99
R1(config-std-nacl)#5 deny 1.1.1.1   (inserts a statement with sequence 5)
```

> [!tip] تحرير الـ numbered ACLs
> يمكنك تحرير الـ numbered ACLs بنفس أسلوب إعداد الـ named ACLs كما في المثال الأخير (editing by sequence numbers).

#### 9.5 التحقق

```text
R1#show ip access-list        (Same as show access-lists)
R1#show ip access-list 101    (Shows only the specified ACL)
```

**show access-lists وفحص التطبيق على الواجهات:** [[#ACL & NAT Troubleshooting]] — اللاب العملي: [[CCNA Theory & Labs]]

### 10) DHCP, NAT & Management
#ccna/dhcp #ccna/nat #ccna/management

#### 10.1 DHCP Server Configuration

Define a DHCP pool and give it a name:

```text
R1(config)#ip dhcp pool MY_POOL
```

Define network and mask to use in this pool and the default gateway:

```text
R1(dhcp-config)#network 192.168.1.0 255.255.255.0
R1(dhcp-config)#default-router 192.168.1.1
```

Define one or more DNS server (OPTIONAL):

```text
R1(dhcp-config)#dns-server 213.131.65.20 8.8.8.8
```

Confine the lease time (OPTIONAL):

```text
R1(dhcp-config)#lease 2   (days)
```

Define one or more scopes of excluded (reserved) addresses (OPTIONAL):

```text
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.100
R1(config)#ip dhcp excluded-address 192.168.1.200 192.168.1.254
```

#### 10.2 Static NAT

```text
R1(config)#interface serial 0/0
R1(config-if)#ip nat outside
R1(config)#interface FastEthernet 1/1
R1(config-if)#ip nat inside
R1(config)#ip nat inside source static 192.168.1.10 200.1.1.1
```

#### 10.3 Dynamic NAT

```text
R1(config)#access-list 3 permit 192.168.1.0 0.0.0.255
R1(config)#ip nat pool PUB 200.1.1.1 200.1.1.6 netmask 255.255.255.248
R1(config)#ip nat inside source list 3 pool PUB
```

(مع تعريف منافذ inside/outside كما في Static NAT — وPAT overload في الفقرة التالية)

#### 10.4 PAT (Overload)

```text
R1(config)#access-list 1 permit 192.168.10.0 0.0.0.255
R1(config)#interface g0/0
R1(config-if)#ip nat inside
R1(config)#interface g0/1
R1(config-if)#ip nat outside
R1(config)#ip nat inside source list 1 interface g0/1 overload
```

#### 10.5 Mgmt Protocols

```text
logging console
logging host 10.1.1.10
ntp server 10.1.1.20
```

(CDP / LLDP: [[#4) STP, EtherChannel & CDP]])

#### 10.6 التحقق

أوامر التحقق من DHCP وNAT والخدمات مع الشرح: [[#DHCP & Network Services]] • [[#ACL & NAT Troubleshooting]]

### 11) Switch Security & Port Security
#ccna/switching #ccna/security

#### 11.1 Port Security (الخيارات الكاملة)

```text
SW1(config-if)#switchport mode access
SW1(config-if)#switchport port-security
SW1(config-if)#switchport port-security maximum 1
SW1(config-if)#switchport port-security violation shutdown   (options: shutdown, protect, restrict)
SW1(config-if)#switchport port-security mac-address 68b5.9965.1195   (options: H.H.H, sticky)
```

> [!note] الخيار sticky
> يجعل المنفذ يتعلم ويضبط تلقائيًا عناوين MAC للأجهزة المتصلة حاليًا.

#### 11.2 Verify & Troubleshoot Port Security

```text
SW1#show port-security
SW1#show port-security interface fa0/5
```

(جدول الـ MAC: [[#2) Interfaces — Router & Switch]])

### 12) WAN — PPP & Frame Relay
#ccna/ppp #ccna/frame-relay

#### 12.1 PPP Configuration

```text
R1(config)#interface serial 0/0
R1(config-if)#encapsulation ppp
```

#### 12.2 PPP Authentication — CHAP

```text
R1(config)#hostname ALPHA
ALPHA(config)#username BETA password XYZ   (shared password — must be the same on both routers)
ALPHA(config)#interface serial 0/0
ALPHA(config-if)#ppp authentication chap
```

#### 12.3 PPP Authentication — PAP

```text
R1(config)#hostname ALPHA
ALPHA(config)#username BETA password XYZ
ALPHA(config)#interface serial 0/0
ALPHA(config-if)#ppp authentication pap
ALPHA(config-if)#ppp pap sent-username ALPHA password XYZ
```

#### 12.4 PPP Verification

| الأمر | الوصف |
| --- | --- |
| R1#show interface s0/0 | Shows the encapsulation type and the control protocols of PPP |
| R1#show run | Useful for viewing the configuration of usernames and passwords used to authenticate PPP |
| R1#debug ppp authentication | Displays the authentication process of PPP in real time |

#### 12.5 Frame Relay — Multipoint (one subnet)

```text
R1(config)#interface serial 0/0
R1(config-if)#ip address 1.1.1.1 255.255.255.0
R1(config-if)#encapsulation frame-relay   (ietf)
R1(config-if)#frame-relay lmi-type ansi   (optional, as discussed with ISP: ansi, cisco, q933a)
R1(config-if)#frame-relay map ip 1.1.1.2 102 broadcast   (ietf)
R1(config-if)#frame-relay map ip 1.1.1.3 103 broadcast

R2(config)#interface serial 0/0
R2(config-if)#ip address 1.1.1.2 255.255.255.0
R2(config-if)#encapsulation frame-relay
R2(config-if)#frame-relay map ip 1.1.1.1 201 broadcast
R2(config-if)#frame-relay map ip 1.1.1.3 201 broadcast

R3(config)#interface serial 0/0
R3(config-if)#ip address 1.1.1.3 255.255.255.0
R3(config-if)#encapsulation frame-relay
R3(config-if)#frame-relay map ip 1.1.1.1 301 broadcast
R3(config-if)#frame-relay map ip 1.1.1.2 301 broadcast
```

#### 12.6 Frame Relay — Point-to-Point Subinterfaces (subnet per subinterface)

```text
R1(config)#interface serial 0/0
R1(config-if)#encapsulation frame-relay
R1(config)#interface serial 0/0.102 point-to-point
R1(config-subif)#ip address 1.1.1.1 255.255.255.0
R1(config-subif)#frame-relay interface-dlci 102
R1(config)#interface serial 0/0.103 point-to-point
R1(config-subif)#ip address 2.2.2.1 255.255.255.0
R1(config-subif)#frame-relay interface-dlci 103

R2(config)#interface serial 0/0
R2(config-if)#encapsulation frame-relay
R2(config)#interface serial 0/0.201 point-to-point
R2(config-subif)#ip address 1.1.1.2 255.255.255.0
R2(config-subif)#frame-relay interface-dlci 201

R3(config)#interface serial 0/0
R3(config-if)#encapsulation frame-relay
R3(config)#interface serial 0/0.301 point-to-point
R3(config-subif)#ip address 2.2.2.2 255.255.255.0
R3(config-subif)#frame-relay interface-dlci 301
```

#### 12.7 Frame Relay Verification

| الأمر | الوصف |
| --- | --- |
| R1#show interfaces serial 0/0 | Shows the encapsulation type |
| R1#show frame-relay PVC | Lists PVC status information |
| R1#show frame-relay map | Lists DLCI to IP mapping |
| R1#show frame-relay lmi | Lists LMI status information |
| R1#debug frame-relay lmi | Displays the content of LMI messages |
| R1#debug frame-relay events | Lists messages about certain Frame Relay events, including Inverse ARP messages |

---

## Part B — Troubleshooting by Symptom

### General Debug Tools
#ccna/troubleshooting

```text
debug ip icmp
undebug all
```

### Start Here
#ccna/troubleshooting #ccna/first-response

> [!question] السيناريو
> A network is down. Before changing anything, collect the basics.
> SYMPTOM: Users report an outage or intermittent connectivity.

- `show ip interface brief` — Which interfaces are up/up? Any wrong or missing IP?
- `show interfaces description` — Which physical ports connect to what?
- `show logging` — Find recent link flaps, reloads and protocol errors.
- `show version` — Check uptime, software release and recent restart clues.
- `show cdp neighbors detail` — Confirm Cisco neighbor, port and management address.
- `show lldp neighbors detail` — Identify peers on mixed-vendor links.
- `show ip route` — Check default route and expected learned routes.
- `ping <destination>` — Test reachability. ICMP blocking can affect results.

> [!quote] START POINT
> Interface status, neighbors, logs and routing basics — before changing anything.

> [!see-also] ذات صلة
> [[#General Debug Tools]] • [[#1) Basic CLI & Device Management]]

### Physical Links & Ports
#ccna/troubleshooting #ccna/interfaces #ccna/physical-layer

> [!question] السيناريو
> The interface is down, flapping or showing packet errors.
> SYMPTOM: Link drops, CRC errors, speed/duplex or err-disable.

- `show interfaces <interface>` — Inspect line state, CRC, input errors, drops and resets.
- `show interfaces status` — Check port status, VLAN, speed and duplex. (Catalyst)
- `show interfaces counters errors` — Find CRC/FCS, alignment and other error counters. (Catalyst)
- `show interfaces status err-disabled` — Locate err-disabled ports. (Supported Catalyst)
- `show interfaces transceiver detail` — Inspect optical diagnostics where supported.
- `show running-config interface <interface>` — Verify shutdown, speed, duplex and port settings.
- `show logging` — Match flaps and err-disable messages to their timestamps.

> [!see-also] ذات صلة
> التجهيز: [[#2) Interfaces — Router & Switch]]

### VLAN & Trunking
#ccna/troubleshooting #ccna/vlans

> [!question] السيناريو
> Hosts connect locally but cannot reach the expected VLAN.
> SYMPTOM: One VLAN works, another is missing across a trunk.

- `show vlan brief` — Is the VLAN created and active? Which access ports use it?
- `show interfaces trunk` — Check trunk state, allowed VLANs and forwarding VLANs.
- `show interfaces <interface> switchport` — Confirm access/trunk mode and native/access VLAN.
- `show interfaces status` — Is the port connected and in the expected VLAN?
- `show mac address-table dynamic vlan <id>` — Are MAC addresses learned in this VLAN?
- `show spanning-tree vlan <id>` — Could STP be blocking the VLAN on a link?
- `show running-config interface <interface>` — Compare the access or trunk configuration on both ends.

> [!see-also] ذات صلة
> التجهيز: [[#3) VLANs, Trunks & VTP]]

### STP & EtherChannel
#ccna/troubleshooting #ccna/stp #ccna/etherchannel

> [!question] السيناريو
> A redundant path is blocked or a port-channel is not forwarding.
> SYMPTOM: Unexpected blocking, topology changes or bundled-link issues.

- `show spanning-tree vlan <id>` — Verify root bridge, roles, state and root path cost.
- `show spanning-tree detail` — Review topology-change counters and port transitions.
- `show spanning-tree root` — See expected root path per spanning-tree instance.
- `show spanning-tree inconsistentports` — Find loop-guard or root-guard inconsistencies if supported.
- `show etherchannel summary` — Confirm Port-channel is up and member ports are bundled.
- `show lacp neighbor` — Is the LACP neighbor detected as expected?
- `show etherchannel <group> detail` — Inspect member ports and aggregation status.

> [!see-also] ذات صلة
> النظرية: [[STP (Spanning Tree Protocol)]] — التجهيز: [[#4) STP, EtherChannel & CDP]]

### IP Reachability & Routing
#ccna/troubleshooting #ccna/routing

> [!question] السيناريو
> The interface is up, but packets cannot reach the destination.
> SYMPTOM: Ping fails, one subnet is unreachable or traffic takes a wrong path.

- `show ip interface brief` — Verify source interface, address and operational status.
- `show ip arp` — Check L3-to-L2 adjacency to directly connected next hops.
- `show ip route <destination>` — Identify the selected route and next hop for this address.
- `show ip route vrf <name>` — Inspect routes in the correct VRF where configured.
- `show ip cef exact-route <src> <dst>` — Check CEF forwarding for a source-destination pair.
- `ping <destination>` — Confirm reachability; lack of replies is not always packet loss.
- `traceroute <destination>` — Identify the path and where probes stop responding.

> [!see-also] ذات صلة
> [[#5) IPv4, Static Routing & RIPv2]] • [[#General Debug Tools]]

### OSPF Troubleshooting
#ccna/troubleshooting #ccna/ospf

> [!question] السيناريو
> OSPF peers fail to form or prefixes are missing.
> SYMPTOM: No neighbors, stuck adjacency or unexpected OSPF paths.

- `show ip ospf neighbor` — Check state, neighbor ID, address and adjacency.
- `show ip ospf neighbor detail` — Inspect full neighbor details and recent state changes.
- `show ip ospf interface brief` — Which interfaces run OSPF? Confirm area and state.
- `show ip ospf interface <interface>` — Compare hello/dead timers, area, network type and auth.
- `show ip ospf database` — Is the expected prefix represented in the LSDB?
- `show ip route ospf` — Which OSPF routes were actually installed?
- `show ip protocols` — Check routing process, passive interfaces and filters.

> [!see-also] ذات صلة
> التجهيز: [[#6) OSPF]] — النظرية: [[CCNA Theory & Labs#4) OSPF LSA Types]]

### EIGRP Troubleshooting
#ccna/troubleshooting #ccna/eigrp

> [!question] السيناريو
> An EIGRP neighbor disappears or routes are not selected.
> SYMPTOM: Missing routes, flapping neighbors or stuck-in-active.

- `show ip eigrp neighbors` — Check peers, hold time, uptime and Q count.
- `show ip eigrp neighbors detail` — Investigate repeated retransmissions or interface issues.
- `show ip eigrp interfaces` — Verify EIGRP is active on the intended interfaces.
- `show ip eigrp topology` — Review successors and feasible successor paths.
- `show ip eigrp topology active` — Find destinations stuck waiting for queries/replies.
- `show ip eigrp topology all-links` — Inspect additional paths not shown in the default view.
- `show ip route eigrp` — Which EIGRP prefixes made it into the routing table?

> [!see-also] ذات صلة
> المرجع: [[#7) EIGRP]]

### BGP Troubleshooting
#ccna/troubleshooting #ccna/bgp

> [!question] السيناريو
> A BGP session will not establish or a route is missing.
> SYMPTOM: Idle/Active peer, unexpected best path, missing advertisements.

- `show ip bgp summary` — Verify peer state, uptime and received prefix count.
- `show ip bgp neighbors <peer>` — Review transport, timers, AS and session diagnostics.
- `show ip bgp` — Inspect the BGP table, attributes and best-path marker.
- `show ip bgp <prefix>` — Check available paths and selected next hop for a prefix.
- `show ip bgp neighbors <peer> advertised-routes` — What is being advertised to this peer?
- `show ip route bgp` — Check whether a BGP route was installed in the RIB.
- `show ip route <next-hop>` — Is the BGP NEXT_HOP reachable via the routing table?

> [!tip] قاعدة ذهبية في BGP
> أكثر سبب شائع لجلسة لا تقوم: الـ ==NEXT_HOP== غير قابل للوصول — افحصه بالأمر 7 قبل أي شيء آخر.

### ACL & NAT Troubleshooting
#ccna/troubleshooting #ccna/acl #ccna/nat

> [!question] السيناريو
> Traffic is routed but blocked, or translations are missing.
> SYMPTOM: A service fails while basic network connectivity still works.

- `show access-lists` — Inspect ACL order, permit/deny entries and hit counts.
- `show ip interface <interface>` — Check inbound/outbound IPv4 ACLs applied to the interface.
- `show running-config interface <interface>` — Confirm ACL attachment and interface role.
- `show ip nat translations` — Are expected source/destination translations present?
- `show ip nat statistics` — Check translation counters, interfaces and pool usage.
- `show running-config | include ip nat` — Locate NAT statements and inside/outside settings.
- `show ip route <destination>` — Ensure reachability exists before blaming ACL/NAT.

> [!see-also] ذات صلة
> التجهيز: [[#9) ACLs — Complete Reference]] • [[#10) DHCP, NAT & Management]]

### DHCP & Network Services
#ccna/troubleshooting #ccna/dhcp #ccna/ntp

> [!question] السيناريو
> Clients fail to get an IP or service visibility is missing.
> SYMPTOM: Address assignment fails or service status looks inconsistent.

- `show ip dhcp pool` — Is the address pool exhausted or configured incorrectly?
- `show ip dhcp binding` — Which client leases exist on the IOS DHCP server?
- `show ip dhcp conflict` — Check duplicate/conflicted addresses.
- `show ip dhcp server statistics` — Review DHCP message counters and abnormal patterns.
- `show ip interface <interface>` — Check interface addressing and configured helper address.
- `show ntp associations` — See time-source reachability and peer status.
- `show ntp status` — Is the local clock synchronized?
- `show logging` — Review errors while reproducing the issue.

> [!see-also] ذات صلة
> التجهيز: [[#10) DHCP, NAT & Management]]

---

## Hands-On Practice
#ccna/labs #ccna/practice

The best way to learn troubleshooting is to practice it on a network that actually runs.

**Skip the time-consuming lab setup** — With Dynamips lab packs, you get:
- Ready-to-import EVE-NG OVA
- Device images already installed
- Guided workbooks for hands-on practice
- Save Config files for your labs

**Master Through Practice:** Use the CCNA Full Pack — ==300+ hands-on labs== aligned with the Cisco Press OCG.

**Free Access:** Get free access by creating an account on our site. Follow the Learn Path to access the free labs.

==Less time installing. More time practicing.==

Start here: [dynamips.io](https://dynamips.io)