# EtherChannel Lab – Packet Tracer

## 🎯 Objective
Configure and verify an **EtherChannel (LACP)** between two multilayer switches in Cisco Packet Tracer.  
Demonstrate **redundancy**, **increased bandwidth**, and **end-to-end connectivity** across bundled links.

---

## 🖥️ Topology

PC0 ---- SW1 ===== SW2 ---- PC1


- **PC0** connected to **SW1** `Fa0/3`  
- **PC1** connected to **SW2** `Fa0/3`  
- **SW1 ↔ SW2** connected via EtherChannel (`Fa0/1–Fa0/5`) presented as **Port-Channel1 (Po1)**

---

## ⚙️ Device Configuration

### PC0
- IP: `192.168.1.10`  
- Subnet Mask: `255.255.255.0`

### PC1
- IP: `192.168.1.20`  
- Subnet Mask: `255.255.255.0`

---

### SW1 Configuration
```bash
enable
configure terminal

! PC0 access port
interface fa0/3
switchport mode access
switchport access vlan 10

! Bundle Fa0/1-5 into EtherChannel using LACP
interface range fa0/1 - 5
switchport trunk encapsulation dot1q
switchport mode trunk
channel-group 1 mode active
no shutdown

! Logical Port-Channel
interface port-channel 1
switchport trunk encapsulation dot1q
switchport mode trunk

end
write memory
SW2 Configuration

enable
configure terminal

! PC1 access port
interface fa0/3
switchport mode access
switchport access vlan 10

! Bundle Fa0/1-5 into EtherChannel using LACP
interface range fa0/1 - 5
switchport trunk encapsulation dot1q
switchport mode trunk
channel-group 1 mode active
no shutdown

! Logical Port-Channel
interface port-channel 1
switchport trunk encapsulation dot1q
switchport mode trunk

end



🔍 Verification
EtherChannel Summary
bash
Copy code
show etherchannel summary
Expected (abridged):


Group  Port-channel  Protocol  Ports
1      Po1(SU)       LACP      Fa0/1(P) Fa0/2(P) Fa0/3(P) Fa0/4(P) Fa0/5(P)
Trunk Verification


show interfaces trunk
Expected: Po1 listed as a trunk carrying VLAN 10.

Spanning Tree Verification

show spanning-tree vlan 10
Expected: Port-channel1 is Forwarding. Member interfaces are suppressed in STP.

🔗 Connectivity Test
Ping PC0 → PC1 ✅ success.

Disconnect one EtherChannel member link → pings still succeed (redundancy).

Reconnect the link → EtherChannel auto-recovers.

✅ Outcome
EtherChannel configured successfully using LACP across 5 links.

All member interfaces are bundled ((P) in EtherChannel summary).

PCs on VLAN 10 maintained connectivity across the EtherChannel.

Redundancy confirmed by simulated link failure and recovery.

📌 Lessons Learned
EtherChannel requires identical settings across member ports (speed, duplex, VLAN, trunking).

Packet Tracer may show misleading port icons (orange/red) — always confirm with CLI (show etherchannel summary).

STP operates on the Port-Channel interface, not on individual member ports.




