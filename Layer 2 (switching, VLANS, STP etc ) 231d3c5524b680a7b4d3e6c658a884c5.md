# Layer 2 (switching, VLANS, STP etc.)

### **CDP**

CDP (Cisco Discovery Protocol) was enabled on each device to gather information such as port identifiers, device names, and platform details. This was especially helpful for correctly configuring individual interfaces as either access or trunk ports, and for verifying active connections on router interfaces.”

e.g CoreSWA, cdp neighbor with ACSW10 - 40 and Router A

##config

#cdp run

![image.png](Layer%202%20(switching,%20VLANS,%20STP%20etc%20)%20231d3c5524b680a7b4d3e6c658a884c5/image.png)

---

### **VLANs and trunks**

Site A includes the following VLANs:

- VLAN10 Management
- VLAN20 HR
- VLAN30 IT
- VLAN40 Sales

Each access switch is configured with a single VLAN and its corresponding ports, which are connected to end hosts (PCs).

e.g ACSW20, Port Fa0/2; Fa0/3 → VLAN20 HR

##config:

#Interface range fa0/2 - fa0/3

#switchporte mode access

#switchport access vlan 20

#show vlan br

![image.png](Layer%202%20(switching,%20VLANS,%20STP%20etc%20)%20231d3c5524b680a7b4d3e6c658a884c5/image%201.png)

---

### **interVLAN routing and trunks**

Since CoreSWA handles inter-VLAN routing, trunk links were established between the access switches and CoreSWA in both directions. These trunks use 802.1Q encapsulation, with VLAN 1 configured as the native VLAN across the network. To optimize performance and maintain efficiency, only the required VLANs are allowed on each trunk.

configuration on CoreSWA:

- Gig1/0/2 = connection to ACSW40
- Gig 1/0/3 = connection to ACSW30
- Gig 1/0/4 = connection to ACSW20
- Gig 1/0/24 = connection to ACSW10

e.g trunk link to ACSW40

##config:

#int gig1/0/2

#switchport mode trunk

#switchport trunk allowed vlan 40

#switchport trunk native vlan 1

![image.png](Layer%202%20(switching,%20VLANS,%20STP%20etc%20)%20231d3c5524b680a7b4d3e6c658a884c5/image%202.png)

As mentioned earlier, SVIs (Switched Virtual Interfaces) were configured on CoreSWA. Each SVI provides a gateway address for the end hosts within its respective subnet/VLAN. Typically, the last usable IP address of each subnet is assigned as the gateway.

e.g SVI for VLAN10 = 192.168.0.62

##config:

#int vlan10

#ip address 192.168.0.62

#no shut

#show ip interface brief

![image.png](Layer%202%20(switching,%20VLANS,%20STP%20etc%20)%20231d3c5524b680a7b4d3e6c658a884c5/image%203.png)

---

### **Spanning Tree Protocol (STP)**

To prevent Layer 2 loops, STP and STP Toolkit were configured on Site A. Additionally, Rapid Spanning Tree Protocol (RSTP) was used to reduce the time between blocking and forwarding states. CoreSWA acts as the root bridge for all VLANs and was therefore configured with a priority of zero.

e.g. config on CoreSWA

##config:

#spanning-tree vlan 10 priority 0

![image.png](Layer%202%20(switching,%20VLANS,%20STP%20etc%20)%20231d3c5524b680a7b4d3e6c658a884c5/image%204.png)

### **STP Toolkit**

The STP Toolkit was applied to multiple ports for different purposes.

Root Guard was configured on every port of CoreSWA that is directly connected to an access switch. The goal was to protect the root bridge (CoreSWA) from unauthorized switches or BPDUs with a higher priority.

e.g. gig1/0/2 link to ACSW1

##config:

#spanning-tree guard root

![image.png](Layer%202%20(switching,%20VLANS,%20STP%20etc%20)%20231d3c5524b680a7b4d3e6c658a884c5/image%205.png)

---

PortFast and BPDU Guard were configured on every access switch for all ports connected to end hosts. This ensures faster link-up times for PCs and helps protect the network against unauthorized switches or devices sending BPDUs.

e.g. fa0/2 on ACSW20

##config

#spanning-tree bpduguard enable

#spanning-tree portfast

![image.png](Layer%202%20(switching,%20VLANS,%20STP%20etc%20)%20231d3c5524b680a7b4d3e6c658a884c5/image%206.png)

---