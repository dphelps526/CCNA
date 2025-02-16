# VLANS (Part 2) - Routing in the LAN: ROAS

## Introduction

### Packet Tracer

[Download Day 17 Lab - VLANS (Part 2)](/JITL/Day%2017%20Lab%20-%20VLANs%20(Part%202).pkt){:download="Day 17 Lab - VLANS (Part 2)"}

### Topology

<figure markdown>
  ![Day 17 Topology](/PT_screenshots/day17.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Configure the switch interfaces connected to PCs as access ports in the correct VLAN.
2. Configure the connection between SW1 and SW2 as a trunk, allowing only the necessary VLANs. Configure an unused VLAN as the native VLAN. *Make sure all necessary VLANs exist on each switch*
3. Configure the connection between SW2 and R1 using 'router on a stick'. Assign the last usable address of each subnet to R1's subinterfaces.

## Answers

??? "1. Configure the switch interfaces connected to PCs as access ports in the correct VLAN."

    === "SW1"
        
        ``` bash
        SW1>en
        SW1#conf t

        SW1(config)#int range f0/1-2
        SW1(config-if-range)#switchport mode access
        SW1(config-if-range)#switchport access vlan 10
        % Access VLAN does not exist. Creating vlan 10

        SW1(config-if-range)#int range f0/3-4
        SW1(config-if-range)#switchport mode acc
        SW1(config-if-range)#sw ac vl 30
        % Access VLAN does not exist. Creating vlan 30
        ```

    === "SW2"
        
        ``` bash
        SW2>en
        SW2#conf t

        SW2(config)#int f0/1
        SW2(config-if)#sw mo ac
        SW2(config-if)#sw ac vl 20
        % Access VLAN does not exist. Creating vlan 20

        SW2(config-if)#int range f0/2-3
        SW2(config-if-range)#sw mo ac
        SW2(config-if-range)#sw ac vl 10
        % Access VLAN does not exist. Creating vlan 10
        ```

??? "2. Configure the connection between SW1 and SW2 as a trunk, allowing only the necessary VLANs. Configure an unused VLAN as the native VLAN. *Make sure all necessary VLANs exist on each switch*"

    === "SW1"
        
        ``` bash
        ! Configure as trunk and setting allowed VLANS
        SW1(config)#int g0/1
        SW1(config-if)#switchport mode trunk
        SW1(config-if)#switchport trunk allowed vlan 10,30

        ! Configure an unused VLAN
        SW1(config-if)#switchport trunk native vlan 1001

        ! Verify VLANS exist on SW1
        SW1(config-if)#end
        SW1#show vlan br

        VLAN Name                             Status    Ports
        ---- -------------------------------- --------- -------------------------------
        1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                        Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                        Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                        Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                        Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                        Gig0/2
        10   VLAN0010                         active    Fa0/1, Fa0/2
        30   VLAN0030                         active    Fa0/3, Fa0/4
        1002 fddi-default                     active    
        1003 token-ring-default               active    
        1004 fddinet-default                  active    
        1005 trnet-default                    active    
        SW1#
        ```

        Note: SW1 doesn't need VLAN20 because if PC5 needs to communicate to any of the computers connected to SW1, its traffic will first be sent to the router which will send it back to SW2 in VLAN10 or VLAN30 which will then send it over to SW1


    === "SW2"
        
        ``` bash
        ! Configure as trunk and setting allowed VLANS
        SW2(config)#int g0/1
        SW2(config-if)#sw mode trunk
        SW2(config-if)#sw trunk allowed vlan 10,30

        ! Configure an unused VLAN
        SW2(config-if)#sw trunk native vlan 1001

        ! Verify VLANS exist on SW2
        SW2(config-if)#end
        SW2#show vlan brief

        VLAN Name                             Status    Ports
        ---- -------------------------------- --------- -------------------------------
        1    default                          active    Fa0/4, Fa0/5, Fa0/6, Fa0/7
                                                        Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                        Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                        Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                        Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                        Fa0/24, Gig0/2
        10   VLAN0010                         active    Fa0/2, Fa0/3
        20   VLAN0020                         active    Fa0/1
        1002 fddi-default                     active    
        1003 token-ring-default               active    
        1004 fddinet-default                  active    
        1005 trnet-default                    active    
        SW2#

        ! Verify the Trunk
        SW2#show int trunk
        Port        Mode         Encapsulation  Status        Native vlan
        Gig0/1      on           802.1q         trunking      1001

        Port        Vlans allowed on trunk
        Gig0/1      10,30

        Port        Vlans allowed and active in management domain
        Gig0/1      10

        Port        Vlans in spanning tree forwarding state and not pruned
        Gig0/1      10

        SW2#
        ```

        "VLANS allowed on trunk" show 10 and 30 which is good but under the "Vlans allowed and active in management domain" we only see VLAN10 which means SW2 won't actually receive VLAN30 traffic on this trunk. This is because VLAN30 doesn't exist yet on the switch. VLAN10 and VLAN20 were automatically created when we configured access ports in those VLANs but VLAN30 wasn't.

        ``` bash
        SW2#
        SW2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        SW2(config)#vlan 30
        SW2(config-vlan)#do show int trunk
        Port        Mode         Encapsulation  Status        Native vlan
        Gig0/1      on           802.1q         trunking      1001

        Port        Vlans allowed on trunk
        Gig0/1      10,30

        Port        Vlans allowed and active in management domain
        Gig0/1      10,30

        Port        Vlans in spanning tree forwarding state and not pruned
        Gig0/1      10,30

        SW2(config-vlan)#
        ```



??? "3. Configure the connection between SW2 and R1 using 'router on a stick'. Assign the last usable address of each subnet to R1's subinterfaces."

    === "SW2"
        
        ``` bash
        SW2#conf t

        ! Configure G0/1 as trunk
        SW2(config)#int g0/2
        SW2(config-if)#sw mode trunk
        SW2(config-if)#sw trunk allowed vlan 10,20,30
        SW2(config-if)#sw trunk native vlan 1001
        SW2(config-if)#end
        SW2#
        ```


    === "R1"
        
        ``` bash
        R1>en
        R1#conf t

        ! First, bring g0/0 up
        R1(config)#int g0/0
        R1(config-if)#no shut

        ! Then access g0/0's sub-interfaces and assign an ip address

        R1(config-if)#int g0/0.10
        R1(config-subif)#encapsulation dot1q 10
        R1(config-subif)#ip address 10.0.0.62 255.255.255.192

        R1(config-subif)#int g0/0.20
        R1(config-subif)#encapsulation dot1q 20
        R1(config-subif)#ip add 10.0.0.126 255.255.255.192

        R1(config-subif)#int g0/0.30
        R1(config-subif)#encapsulation dot1q 30
        R1(config-subif)#ip add 10.0.0.190 255.255.255.192
        ```

        Note: Best practice is to number the sub-interface the same as the VLAN

??? tip "Verify ROAS on R1"

    ``` bash

    R1#show ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
        D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
        N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
        E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
        i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
        * - candidate default, U - per-user static route, o - ODR
        P - periodic downloaded static route

    Gateway of last resort is not set

        10.0.0.0/8 is variably subnetted, 6 subnets, 2 masks
    C       10.0.0.0/26 is directly connected, GigabitEthernet0/0.10
    L       10.0.0.62/32 is directly connected, GigabitEthernet0/0.10
    C       10.0.0.64/26 is directly connected, GigabitEthernet0/0.20
    L       10.0.0.126/32 is directly connected, GigabitEthernet0/0.20
    C       10.0.0.128/26 is directly connected, GigabitEthernet0/0.30
    L       10.0.0.190/32 is directly connected, GigabitEthernet0/0.30

    R1#

    ```

    Look for the connected routes

## Another way of looking at ROAS

<figure markdown>
  ![ROAS](/PT_screenshots/day17_helper.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

## Commands

* `show ip route` - helps verify ROAS configuration
  