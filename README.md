# Enterprise Network Lab
![Enterprise Network Topology](./Images/TOPOLOGY.png)

# Addressing for Reference
![Connected Interfaces](./Images/Connected_Interfaces.png)

## Lab Overview
- This project simulates a small-to-medium enterprise network environment using GNS3.
- **OSPF** on R1, MLS-1, MLS-2 
- **HSRP** on MLS-1 and MLS-2 (MLS-1 is the Active router in this topology)
- Layer 3 **LACP** between the MLS-1 and MLS-2
- **SVIs** on MLS-1 and MLS-2
- **DHCP** and **DNS** running on the Server (MLS-1 and MLS-2 are configured as Relays for all VLANs)
- **VTP** between Switches and MLS

---

# Key Configuration Commands

## SVIs on MLS-1
```shell
R1(config)# interface Vlan 10
R1(config-vlan)# ip address 10.0.10.2 255.255.255.0
R1(config-vlan)# no shutdown
R1(config-vlan)# exit
R1(config)# interface Vlan 20
R1(config-vlan)# ip address 10.0.20.2 255.255.255.0
R1(config-vlan)# no shutdown
R1(config-vlan)# exit
R1(config)# interface Vlan 30
R1(config-vlan)# ip address 10.0.30.2 255.255.255.0
R1(config-vlan)# no shutdown
R1(config-vlan)# exit
R1(config)# interface Vlan 40
R1(config-vlan)# ip address 10.0.40.2 255.255.255.0
R1(config-vlan)# no shutdown
R1(config-vlan)# exit
R1(config)# interface Vlan 50
R1(config-vlan)# ip address 10.0.100.1 255.255.255.0
R1(config-vlan)# no shutdown
R1(config-vlan)# exit
```

![SVIs on MLS-1](./Images/MLS1_show_ip_interface_brief.png)

## HSRP on MLS-1
```shell
MLS-1(config)# interface Vlan 10
MLS-1(config-vlan)# standby version 2
MLS-1(config-vlan)# standby ip 10.0.10.1
MLS-1(config-vlan)# standby preempt
MLS-1(config-vlan)# standby priority 105
MLS-1(config)# interface Vlan 20
MLS-1(config-vlan)# standby version 2
MLS-1(config-vlan)# standby 2 ip 10.0.20.1
MLS-1(config-vlan)# standby 2 preempt
MLS-1(config-vlan)# standby 2 priority 105
MLS-1(config)# interface Vlan 30
MLS-1(config-vlan)# standby version 2
MLS-1(config-vlan)# standby 3 ip 10.0.30.1
MLS-1(config-vlan)# standby 3 preempt
MLS-1(config-vlan)# standby 3 priority 105
MLS-1(config)# interface Vlan 40
MLS-1(config-vlan)# standby version 2
MLS-1(config-vlan)# standby 4 ip 10.0.40.1
MLS-1(config-vlan)# standby 4 preempt
MLS-1(config-vlan)# standby 4 priority 105
```

![Show Standby Brief on MLS-1](./Images/MLS1_show_standby_brief.png)

## LACP on MLS-1
```shell
MLS-1(config)# interface range g1/0-3
MLS-1(config-if-range)# no shutdown
MLS-1(config-if-range)# no switchport
MLS-1(config-if-range)# channel-group 1 mode active
MLS-1(config)#exit
MLS-1(config-int)# interface port-channel 1
MLS-1(config-int)# ip address 192.168.1.1 255.255.255.252
```

## LACP on MLS-2
```shell
R2(config)# interface range g1/0-3
R2(config-if-range)# no shutdown
R2(config-if-range)# no switchport
R2(config-if-range)# channel-group 1 mode passive
R2(config)#exit
R2(config-int)# interface port-channel 1
R2(config-int)# ip address 192.168.1.2 255.255.255.252
```

![show etherchannel summary on MLS-1](./Images/MLS1_show_etherchannel_summary.png)

---

![show etherchannel summary on MLS-2](./Images/MLS2_show_etherchannel_summary.png)

## OSPF on R1
```shell
R1(config)# router ospf 1
R1(config-router)# network 172.16.1.0 0.0.0.3 area 0
R1(config-router)# network 172.16.2.0 0.0.0.3 area 0
R1(config-router)# network 203.39.10.0 0.0.0.3 area 0
```

![show ip route on R1](./Images/R1_show_ip_route.png)

## OSPF on MLS-1
```shell
MLS-1(config)# router ospf 1
MLS-1(config-router)# network 10.0.10.0 0.0.0.255 area 0
MLS-1(config-router)# network 10.0.20.0 0.0.0.255 area 0
MLS-1(config-router)# network 10.0.30.0 0.0.0.255 area 0
MLS-1(config-router)# network 10.0.40.0 0.0.0.255 area 0
MLS-1(config-router)# network 10.0.100.0 0.0.0.255 area 0
MLS-1(config-router)# network 172.16.1.0 0.0.0.3 area 0
MLS-1(config-router)# network 192.168.1.0 0.0.0.3 area 0
MLS-1(config-router)# passive-interface G0/0, G0/1, G0/2
```

![show ip route on MLS-1](./Images/MLS1_show_ip_route.png)

## OSPF on MLS-2
```shell
MLS-2(config)# router ospf 1 
MLS-2(config-router)# network 10.0.10.0 0.0.0.255 area 0
MLS-2(config-router)# network 10.0.20.0 0.0.0.255 area 0
MLS-2(config-router)# network 10.0.30.0 0.0.0.255 area 0
MLS-2(config-router)# network 10.0.40.0 0.0.0.255 area 0
MLS-2(config-router)# network 172.16.2.0 0.0.0.3 area 0
MLS-2(config-router)# network 192.168.1.0 0.0.0.3 area 0
MLS-2(config-router)# passive-interface G0/0, G0/1, G0/2
```

![show ip route on MLS-2](./Images/MLS2_show_ip_route.png)

## PAT on R1
```shell
R1(config)# access-list 1 permit 10.0.0.0 0.255.255.255
R1(config)# ip nat inside source list 1 interface g2/0 overload
R1(config)# interface g0/0
R1(config-if)# ip nat inside
R1(config-if)# interface g1/0
R1(config-if)# ip nat inside
R1(config-if)# interface g2/0
R1(config-if)# ip nat outside
```

### PAT Translations whilst PCs were pinging the ISP-1 router
![PAT Translations](./Images/R1_show_ip_nat_translations.png)

## Extended ACL Configuration on MLS-1
```shell
MLS-1(config)# ip access-list extended HR_TO_ACCOUNTING
MLS-1(config-ext-nacl)# deny ip 10.0.10.0 0.0.0.255 10.0.30.0 0.0.0.255
MLS-1(config-ext-nacl)# permit ip any any
MLS-1(config)# ip access-list extended IT_TO_SALES
MLS-1(config-ext-nacl)# deny ip 10.0.20.0 0.0.0.255 10.0.40.0 0.0.0.255
MLS-1(config-ext-nacl)# permit ip any any
MLS-1(config)# interface g0/0
MLS-1(config-if)# ip access-group HR_TO_ACCOUNTING in
MLS-1(config)# interface g0/1
MLS-1(config-if)# ip access-group IT_TO_SALES in
```

## VTP configuration on SW1
```shell
SW1(config)# vtp mode server
SW1(config)# vtp domain Kostas
```

(no need to configured vtp on other devices manually since it automatically spreads to the rest)

## DHCP Configurations 
```shell
DHCP-DNS(config)# ip dhcp pool POOL_VLAN10
DHCP-DNS(dhcp-config)# network 10.0.10.0 255.255.255.0
DHCP-DNS(dhcp-config)# default-router 10.0.10.1
DHCP-DNS(dhcp-config)# dns-server 10.0.100.10
DHCP-DNS(config)# ip dhcp pool POOL_VLAN20
DHCP-DNS(dhcp-config)# network 10.0.20.0 255.255.255.0
DHCP-DNS(dhcp-config)# default-router 10.0.20.1
DHCP-DNS(dhcp-config)# dns-server 10.0.100.10
DHCP-DNS(config)# ip dhcp pool POOL_VLAN30
DHCP-DNS(dhcp-config)# network 10.0.30.0 255.255.255.0
DHCP-DNS(dhcp-config)# default-router 10.0.30.1
DHCP-DNS(dhcp-config)# dns-server 10.0.100.10
DHCP-DNS(config)# ip dhcp pool POOL_VLAN40
DHCP-DNS(dhcp-config)# network 10.0.40.0 255.255.255.0
DHCP-DNS(dhcp-config)# default-router 10.0.40.1
DHCP-DNS(dhcp-config)# dns-server 10.0.100.10
DHCP-DNS(config)#ip domain-lookup
DHCP-DNS(config)# ip host PC1 10.0.10.4
DHCP-DNS(config)# ip host PC2 10.0.10.5
DHCP-DNS(config)# ip host PC3 10.0.20.4
DHCP-DNS(config)# ip host PC4 10.0.20.5
DHCP-DNS(config)# ip host PC5 10.0.30.5
DHCP-DNS(config)# ip host PC6 10.0.30.4
DHCP-DNS(config)# ip host PC7 10.0.40.4
DHCP-DNS(config)# ip host PC8 10.0.40.5
```
