# Configuring Static Routes

## Introduction

### Packet Tracer

[Download Day 11 Lab - Configuring Static Routes](/JITL/Day%2011%20Lab%20-%20Configuring%20Static%20Routes.pkt){:download="Day 11 Lab - Configuring Static Routes"}

### Topology

<figure markdown>
  ![Day 11 Topology](/PT_screenshots/day11.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Configure the PCs and routers according to the network diagram (hostnames, IP addresses, etc.) Remember to configure the gateway on the PCs. (You don't have to configure the switches)
2. Configure static routes on the routers to enable PC1 to successfully ping PC2.

## Answers

??? "1. Configure the PCs and routers according to the network diagram"

    === "R1"
        
        ``` bash
        Router>en
        Router#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R1
        R1(config)#int g0/1
        R1(config-if)#ip add 192.168.1.254 255.255.255.0
        R1(config-if)#no shut

        R1(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
        int g0/0
        R1(config-if)#ip add 192.168.12.1 255.255.255.0
        R1(config-if)#no shut

        R1(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        R1#
        ```

    === "R2"
        
        ``` bash
        Router>en
        Router#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R2
        R2(config)#int g0/0
        R2(config-if)#ip add 192.168.12.2 255.255.255.0
        R2(config-if)#no shut

        R2(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
        int g0/1
        R2(config-if)#ip add 192.168.13.2 255.255.255.0
        R2(config-if)#no shut

        R2(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

        R2(config-if)#
        ```

    === "R3"
        
        ``` bash
        Router>en
        Router#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R3
        R3(config)#int g0/0
        R3(config-if)#ip add 192.168.13.3 255.255.255.0
        R3(config-if)#no shut

        R3(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
        int g0/1
        R3(config-if)#ip add 192.168.3.254 255.255.255.0
        R3(config-if)#no shut

        R3(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

        R3(config-if)#
        ```

??? "2. Configure static routes on the routers to enable PC1 to successfully ping PC2."

    === "R1"
        
        R1 needs a route to the 192.168.3.0/24 (yellow) network:
        ``` bash
        R1(config)#ip route 192.168.3.0 255.255.255.0 192.168.12.2
        ```

        Confirm that the route is in the routing table:
        ``` bash
        R1(config)#do sh ip route
        Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
            D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
            N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
            E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
            i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
            * - candidate default, U - per-user static route, o - ODR
            P - periodic downloaded static route

        Gateway of last resort is not set

            192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
        C       192.168.1.0/24 is directly connected, GigabitEthernet0/1
        L       192.168.1.254/32 is directly connected, GigabitEthernet0/1
        S    192.168.3.0/24 [1/0] via 192.168.12.2
            192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
        C       192.168.12.0/24 is directly connected, GigabitEthernet0/0
        L       192.168.12.1/32 is directly connected, GigabitEthernet0/0

        R1(config)#
        ```

        Note: The static route that was just added is indicated by the 'S'. [1/0] is [AD/Metric]. Static Routes have an AD of 1

        Note: The local route (L) is just the address configured on the interface

    === "R2"
        
        R2 needs a route to the 192.168.3.0/24 (yellow) network and 1.0/24 (blue) network:
        ``` bash
        ! Configured with next hop
        R2(config)#ip route 192.168.1.0 255.255.255.0 192.168.12.1

        ! Configured with interface
        R2(config)#ip route 192.168.3.0 255.255.255.0 g0/1
        ```

        Confirm that the route is in the routing table:
        ``` bash
        R2(config)#do sh ip route
        Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
            D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
            N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
            E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
            i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
            * - candidate default, U - per-user static route, o - ODR
            P - periodic downloaded static route

        Gateway of last resort is not set
        ! Next hop static route
        S    192.168.1.0/24 [1/0] via 192.168.12.1

        ! Interface static route
        S    192.168.3.0/24 is directly connected, GigabitEthernet0/1
            192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
        C       192.168.12.0/24 is directly connected, GigabitEthernet0/0
        L       192.168.12.2/32 is directly connected, GigabitEthernet0/0
            192.168.13.0/24 is variably subnetted, 2 subnets, 2 masks
        C       192.168.13.0/24 is directly connected, GigabitEthernet0/1
        L       192.168.13.2/32 is directly connected, GigabitEthernet0/1

        R2(config)#
        ```

    === "R3"
        
        R3 needs a route to the 192.168.1.0/24 (blue) network:
        ``` bash
        R3(config)#ip route 192.168.1.0 255.255.255.0 192.168.13.2
        ```

        Confirm that the route is in the routing table:
        ``` bash
        R3(config)#do sh ip route
        Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
            D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
            N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
            E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
            i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
            * - candidate default, U - per-user static route, o - ODR
            P - periodic downloaded static route

        Gateway of last resort is not set

        S    192.168.1.0/24 [1/0] via 192.168.13.2
            192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
        C       192.168.3.0/24 is directly connected, GigabitEthernet0/1
        L       192.168.3.254/32 is directly connected, GigabitEthernet0/1
            192.168.13.0/24 is variably subnetted, 2 subnets, 2 masks
        C       192.168.13.0/24 is directly connected, GigabitEthernet0/0
        L       192.168.13.3/32 is directly connected, GigabitEthernet0/0

        R3(config)#
        ```

??? tip "Putting it all together"

    === "R1"
        
        Commands
        ``` bash
        Router>enable
        Router#configure terminal
        Router(config)#hostname R1
        R1(config)#int g0/1
        R1(config-if)#ip add 192.168.1.254 255.255.255.0
        R1(config-if)#no shut
        R1(config-if)#int g0/0
        R1(config-if)#ip add 192.168.12.1 255.255.255.0
        R1(config-if)#no shut
        R1(config-if)#exit
        R1(config)#ip route 192.168.3.0 255.255.255.0 192.168.12.2
        R1(config)#
        ```
        With comments
        ``` bash
        ! Enter privileged exec mode
        R1>enable

        ! Enter global configuration mode
        R1#conf t

        ! Set the hostname to R1
        R1(config)#hostname R1

        ! Enter the configuration mode for GigabitEthernet 0/1 interface
        R1(config)#int g0/1

        ! Assign IP address and subnet mask to GigabitEthernet 0/1 interface
        R1(config-if)#ip add 192.168.1.254 255.255.255.0

        ! Enable the GigabitEthernet 0/1 interface
        R1(config-if)#no shut

        ! Enter the configuration mode for GigabitEthernet 0/0 interface
        R1(config-if)#int g0/0

        ! Assign IP address and subnet mask to GigabitEthernet 0/0 interface
        R1(config-if)#ip add 192.168.12.1 255.255.255.0

        ! Enable the GigabitEthernet 0/0 interface
        R1(config-if)#no shut

        ! Exit the interface configuration mode and go back to global configuration mode
        R1(config-if)#exit

        ! Set up a static route for traffic destined for 192.168.3.0/24 via next-hop IP 192.168.12.2
        R1(config)#ip route 192.168.3.0 255.255.255.0 192.168.12.2

        ! Exit global configuration mode
        R1(config)#end

        ! Write the configuration to memory
        R1#wr

        ! Display a message indicating successful configuration save
        Building configuration...
        [OK]

        ```

    === "R2"
        
        Commands
        ``` bash
        R2>en
        R2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R2(config)#hostname R2
        R2(config)#int g0/0
        R2(config-if)#ip add 192.168.12.2 255.255.255.0
        R2(config-if)#no shut
        R2(config-if)#int g0/1
        R2(config-if)#ip add 192.168.13.2 255.255.255.0
        R2(config-if)#no shut
        R2(config-if)#ip route 192.168.1.0 255.255.255.0 192.168.12.1
        R2(config)#ip route 192.168.3.0 255.255.255.0 g0/1
        %Default route without gateway, if not a point-to-point interface, may impact performance
        R2(config)#end
        R2#
        %SYS-5-CONFIG_I: Configured from console by console
        wr
        Building configuration...
        [OK]
        R2#
        ```

        With comments
        ``` bash
        ! Enter privileged exec mode
        R2>en

        ! Enter global configuration mode
        R2#conf t

        ! Set the hostname to R2
        R2(config)#hostname R2

        ! Enter the configuration mode for GigabitEthernet 0/0 interface
        R2(config)#int g0/0

        ! Assign IP address and subnet mask to GigabitEthernet 0/0 interface
        R2(config-if)#ip add 192.168.12.2 255.255.255.0

        ! Enable the GigabitEthernet 0/0 interface
        R2(config-if)#no shut

        ! Enter the configuration mode for GigabitEthernet 0/1 interface
        R2(config-if)#int g0/1

        ! Assign IP address and subnet mask to GigabitEthernet 0/1 interface
        R2(config-if)#ip add 192.168.13.2 255.255.255.0

        ! Enable the GigabitEthernet 0/1 interface
        R2(config-if)#no shut

        ! Set up a static route for traffic destined for 192.168.1.0/24 via next-hop IP 192.168.12.1
        R2(config-if)#ip route 192.168.1.0 255.255.255.0 192.168.12.1

        ! Set up a static route for traffic destined for 192.168.3.0/24 via the next-hop interface GigabitEthernet 0/1
        R2(config)#ip route 192.168.3.0 255.255.255.0 g0/1
        ! Note: The warning "Default route without gateway..." might indicate that a default route is not specified.

        ! Exit global configuration mode
        R2(config)#end

        ! Save the configuration to memory
        R2#wr

        ! Display a message indicating successful configuration save
        R2#Building configuration...
        R2#[OK]
        ```

    === "R3"
        
        ``` bash
        Router>en
        Router#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R3
        R3(config)#int g0/0
        R3(config-if)#ip add 192.168.13.3 255.255.255.0
        R3(config-if)#no shut

        R3(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
        int g0/1
        R3(config-if)#ip add 192.168.3.254 255.255.255.0
        R3(config-if)#no shut

        R3(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

        R3(config-if)#
        ```

## Commands

* `ip address 192.168.3.0 255.255.255.0 192.168.12.2` - Setting static route by next hop
* `ip address 192.168.3.0 255.255.255.0 g0/1` - Setting static route by interface
  