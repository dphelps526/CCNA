# OSPF (Part 3)

## Introduction

### Packet Tracer

[Download Day 28 Lab - OSPF (Part 3)](/JITL/Day%2028%20Lab%20-%20OSPF%20(Part%203).pkt){:download="Day 28 Lab - OSPF (Part 3)}

### Topology

<figure markdown>
  ![Day 28 Topology](/PT_screenshots/day28.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

The network has been pre-configured (IP addresses, OSPF)

1. The connection between R1 and R2 has been newly added.
  -Configure the serial connection between R1 and R2 (clock rate of 128000).
  -Configure OSPF on R1 and R2.

2. Only R3 has a route to 10.0.2.0/24.  Why?  Fix the problem.

3. R2 and R4 won't become OSPF neighbors with R5.  Why?  Fix the problem.

4. PC1 and PC2 can't ping the external server 8.8.8.8.  Why?  Fix the problem.

## Answers


??? "1. The connection between R1 and R2 has been newly added. Configure the serial connection between R1 and R2 (clock rate of 128000). Configure OSPF on R1 and R2."

    === "R1"

        ``` bash
        R1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R1(config)#int s0/0/0
        R1(config-if)#ip add 192.168.12.1 255.255.255.252
        ```

        ??? abstract "Clock Rate is set on the DCE side, let's check this:"

            ``` bash
            R1(config-if)#do show controllers s0/0/0

            Interface Serial0/0/0
            Hardware is PowerQUICC MPC860
            DCE V.35, clock rate 2000000
            idb at 0x81081AC4, driver data structure at 0x81084AC0
            SCC Registers:
            General [GSMR]=0x2:0x00000000, Protocol-specific [PSMR]=0x8
            Events [SCCE]=0x0000, Mask [SCCM]=0x0000, Status [SCCS]=0x00
            Transmit on Demand [TODR]=0x0, Data Sync [DSR]=0x7E7E
            Interrupt Registers:
            Config [CICR]=0x00367F80, Pending [CIPR]=0x0000C000
            Mask   [CIMR]=0x00200000, In-srv  [CISR]=0x00000000
            Command register [CR]=0x580
            Port A [PADIR]=0x1030, [PAPAR]=0xFFFF
                [PAODR]=0x0010, [PADAT]=0xCBFF
            --More-- 
            ```

            This says DCE in the third line so let's set the clock rate here.

        ``` bash
        R1(config-if)#clock rate 128000
        R1(config-if)#no shut
        ```

        ??? abstract "Let's see if OSPF is already on this router"

            ``` bash
            R1#show ip prot
            R1#
            ```

        No, so let's do that.

        ``` bash
        R1(config)#int s0/0/0
        R1(config-if)#ip ospf 1 area 0
        R1(config-if)#int g0/0
        R1(config-if)#ip ospf 1 area 0
        ```

        ??? abstract "Let's check the routing table to confirm"

            ``` bash
            R1(config-if)#do show ip route
            Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
                D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
                N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
                E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
                i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
                * - candidate default, U - per-user static route, o - ODR
                P - periodic downloaded static route

            Gateway of last resort is not set

                10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
            C       10.0.1.0/24 is directly connected, GigabitEthernet0/0
            L       10.0.1.254/32 is directly connected, GigabitEthernet0/0
                192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
            C       192.168.12.0/30 is directly connected, Serial0/0/0
            L       192.168.12.1/32 is directly connected, Serial0/0/0
                192.168.34.0/30 is subnetted, 1 subnets
            O       192.168.34.0/30 [110/66] via 192.168.12.2, 00:02:50, Serial0/0/0
                192.168.245.0/29 is subnetted, 1 subnets
            O       192.168.245.0/29 [110/65] via 192.168.12.2, 00:02:50, Serial0/0/0

            R1(config-if)#
            ```

    === "R2"

        ``` bash
        R2>en
        R2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R2(config)#int s0/0/0
        R2(config-if)#ip add 192.168.12.2 255.255.255.252
        R2(config-if)#no shut
        ```

        ??? abstract "Quickly confirm that this is the DTE side"

            ``` bash
            R2(config-if)#do show controllers s0/0/0
            Interface Serial0/0/0
            Hardware is PowerQUICC MPC860
            DTE V.35 TX and RX clocks detected
            idb at 0x81081AC4, driver data structure at 0x81084AC0
            SCC Registers:
            General [GSMR]=0x2:0x00000000, Protocol-specific [PSMR]=0x8
            Events [SCCE]=0x0000, Mask [SCCM]=0x0000, Status [SCCS]=0x00
            Transmit on Demand [TODR]=0x0, Data Sync [DSR]=0x7E7E
            Interrupt Registers:
            Config [CICR]=0x00367F80, Pending [CIPR]=0x0000C000
            Mask   [CIMR]=0x00200000, In-srv  [CISR]=0x00000000
            Command register [CR]=0x580
            Port A [PADIR]=0x1030, [PAPAR]=0xFFFF
                [PAODR]=0x0010, [PADAT]=0xCBFF
            Port B [PBDIR]=0x09C0F, [PBPAR]=0x0800E
                [PBODR]=0x00000, [PBDAT]=0x3FFFD
            Port C [PCDIR]=0x00C, [PCPAR]=0x200
                [PCSO]=0xC20,  [PCDAT]=0xDF2, [PCINT]=0x00F
            Receive Ring
                    rmd(68012830): status 9000 length 60C address 3B6DAC4
                    rmd(68012838): status B000 length 60C address 3B6D444
            Transmit Ring
            --More-- 
            ```

            Now let's enable OSPF

        ??? abstract "Let's see if OSPF is already on this router"

            ``` bash
            R2(config-if)#do show ip prot

            Routing Protocol is "ospf 1"
            Outgoing update filter list for all interfaces is not set 
            Incoming update filter list for all interfaces is not set 
            Router ID 192.168.245.1
            Number of areas in this router is 1. 1 normal 0 stub 0 nssa
            Maximum path: 4
            Routing for Networks:
                192.168.245.1 0.0.0.0 area 0
            Routing Information Sources:  
                Gateway         Distance      Last Update 
                192.168.34.1         110      00:00:06
                192.168.245.1        110      00:00:06
                192.168.245.2        110      00:00:07
            Distance: (default is 110)

            R2(config-if)#end
            ```

        So let's place the new interface into it

        ``` bash
        R2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R2(config)#int s0/0/0
        R2(config-if)#ip ospf 1 area 0
        R2(config-if)#
        ```






??? "2. Only R3 has a route to 10.0.2.0/24.  Why?  Fix the problem."

    Maybe R3 didn't form a relationship with R4? Check this on R4

    ``` bash
    R4>en
    R4#show ip ospf nei


    Neighbor ID     Pri   State           Dead Time   Address         Interface
    192.168.245.1     1   FULL/BDR        00:00:38    192.168.245.1   GigabitEthernet0/0
    192.168.34.1      1   FULL/BDR        00:00:39    192.168.34.1    GigabitEthernet0/1
    R4#
    ```

    That's not it, R3 and R4 have full OSPF adjacency. Is R4 learning a route to 10.0.2.0?

    ``` bash
    R4#show ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
        D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
        N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
        E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
        i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
        * - candidate default, U - per-user static route, o - ODR
        P - periodic downloaded static route

    Gateway of last resort is not set

        10.0.0.0/24 is subnetted, 1 subnets
    O       10.0.1.0/24 [110/66] via 192.168.245.1, 00:10:39, GigabitEthernet0/0
        192.168.12.0/30 is subnetted, 1 subnets
    O       192.168.12.0/30 [110/65] via 192.168.245.1, 00:14:47, GigabitEthernet0/0
        192.168.34.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.34.0/30 is directly connected, GigabitEthernet0/1
    L       192.168.34.2/32 is directly connected, GigabitEthernet0/1
        192.168.245.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.245.0/29 is directly connected, GigabitEthernet0/0
    L       192.168.245.2/32 is directly connected, GigabitEthernet0/0

    R4#
    ```

    No it's not. Let's check the network type on R4.

    ``` bash
    R4#show ip ospf int g0/1

    GigabitEthernet0/1 is up, line protocol is up
    Internet address is 192.168.34.2/30, Area 0
    Process ID 1, Router ID 192.168.245.2, Network Type BROADCAST, Cost: 1
    Transmit Delay is 1 sec, State DR, Priority 1
    Designated Router (ID) 192.168.245.2, Interface address 192.168.34.2
    Backup Designated Router (ID) 192.168.34.1, Interface address 192.168.34.1
    Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
        Hello due in 00:00:05
    Index 2/2, flood queue length 0
    Next 0x0(0)/0x0(0)
    Last flood scan length is 1, maximum is 1
    Last flood scan time is 0 msec, maximum is 0 msec
    Neighbor Count is 1, Adjacent neighbor count is 1
        Adjacent with neighbor 192.168.34.1  (Backup Designated Router)
    Suppress hello for 0 neighbor(s)
    R4#
    ```

    BROADCAST is fine, lets check R3

    ``` bash
    R3#show ip ospf int g0/1

    GigabitEthernet0/1 is up, line protocol is up
    Internet address is 192.168.34.1/30, Area 0
    Process ID 1, Router ID 192.168.34.1, Network Type POINT-TO-POINT, Cost: 1
    Transmit Delay is 1 sec, State POINT-TO-POINT,
    Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
        Hello due in 00:00:03
    Index 1/1, flood queue length 0
    Next 0x0(0)/0x0(0)
    Last flood scan length is 1, maximum is 1
    Last flood scan time is 0 msec, maximum is 0 msec
    Neighbor Count is 1 , Adjacent neighbor count is 1
        Adjacent with neighbor 192.168.245.2
    Suppress hello for 0 neighbor(s)
    R3#
    ```

    POINT-TO-POINT which isn't necessarily a problem but both sides need to match

    ``` bash
    R3(config-if)#no ip ospf network point-to
    R3(config-if)#
    10:49:13: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.245.2 on GigabitEthernet0/1 from FULL to DOWN, Neighbor Down: Interface down or detached

    10:49:13: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.245.2 on GigabitEthernet0/1 from LOADING to FULL, Loading Done

    R3(config-if)#
    ```
    Go back to R4 and see if it learned that route now

    ``` bash
    R4#show ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
        D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
        N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
        E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
        i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
        * - candidate default, U - per-user static route, o - ODR
        P - periodic downloaded static route

    Gateway of last resort is not set

        10.0.0.0/24 is subnetted, 2 subnets
    O       10.0.1.0/24 [110/66] via 192.168.245.1, 00:14:08, GigabitEthernet0/0
    O       10.0.2.0/24 [110/2] via 192.168.34.1, 00:00:29, GigabitEthernet0/1
        192.168.12.0/30 is subnetted, 1 subnets
    O       192.168.12.0/30 [110/65] via 192.168.245.1, 00:18:16, GigabitEthernet0/0
        192.168.34.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.34.0/30 is directly connected, GigabitEthernet0/1
    L       192.168.34.2/32 is directly connected, GigabitEthernet0/1
        192.168.245.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.245.0/29 is directly connected, GigabitEthernet0/0
    L       192.168.245.2/32 is directly connected, GigabitEthernet0/0

    R4#
    ```

??? "3. R2 and R4 won't become OSPF neighbors with R5.  Why?  Fix the problem."

    Let's check some settings on the interfaces in the 192.168.245.0/29 subnet. First, R2

    ``` bash
    R2#show ip ospf int g0/0

    GigabitEthernet0/0 is up, line protocol is up
    Internet address is 192.168.245.1/29, Area 0
    Process ID 1, Router ID 192.168.245.1, Network Type BROADCAST, Cost: 1
    Transmit Delay is 1 sec, State BDR, Priority 1
    Designated Router (ID) 192.168.245.2, Interface address 192.168.245.2
    Backup Designated Router (ID) 192.168.245.1, Interface address 192.168.245.1
    Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
        Hello due in 00:00:02
    Index 1/1, flood queue length 0
    Next 0x0(0)/0x0(0)
    Last flood scan length is 1, maximum is 1
    Last flood scan time is 0 msec, maximum is 0 msec
    Neighbor Count is 1, Adjacent neighbor count is 1
        Adjacent with neighbor 192.168.245.2  (Designated Router)
    Suppress hello for 0 neighbor(s)
    R2#
    ```

    IP address is correct, we're in area 0, and the timers are default. Check R4

    ``` bash
    R4#show ip ospf int g0/0

    GigabitEthernet0/0 is up, line protocol is up
    Internet address is 192.168.245.2/29, Area 0
    Process ID 1, Router ID 192.168.245.2, Network Type BROADCAST, Cost: 1
    Transmit Delay is 1 sec, State DR, Priority 1
    Designated Router (ID) 192.168.245.2, Interface address 192.168.245.2
    Backup Designated Router (ID) 192.168.245.1, Interface address 192.168.245.1
    Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
        Hello due in 00:00:08
    Index 1/1, flood queue length 0
    Next 0x0(0)/0x0(0)
    Last flood scan length is 1, maximum is 1
    Last flood scan time is 0 msec, maximum is 0 msec
    Neighbor Count is 1, Adjacent neighbor count is 1
        Adjacent with neighbor 192.168.245.1  (Backup Designated Router)
    Suppress hello for 0 neighbor(s)
    R4#
    ```

    Same for R4. Check R5

    ``` bash
    R5#show ip ospf int g0/0

    GigabitEthernet0/0 is up, line protocol is up
    Internet address is 192.168.245.3/29, Area 0
    Process ID 1, Router ID 203.0.113.1, Network Type BROADCAST, Cost: 1
    Transmit Delay is 1 sec, State DR, Priority 1
    Designated Router (ID) 203.0.113.1, Interface address 192.168.245.3
    No backup designated router on this network
    Timer intervals configured, Hello 5, Dead 20, Wait 20, Retransmit 5
        Hello due in 00:00:04
    Index 1/1, flood queue length 0
    Next 0x0(0)/0x0(0)
    Last flood scan length is 1, maximum is 1
    Last flood scan time is 0 msec, maximum is 0 msec
    Neighbor Count is 0, Adjacent neighbor count is 0
    Suppress hello for 0 neighbor(s)
    R5#
    ```

    Timers are not default. Lets fix this.

    ``` bash
    R5#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R5(config)#int g0/0
    R5(config-if)#no ip ospf hell
    R5(config-if)#no ip ospf hello-interval 
    R5(config-if)#no ip ospf dea
    R5(config-if)#no ip ospf dead-interval 
    R5(config-if)#
    ```

    Now we can check the neighbor tables

    ```bash
    R5#show ip ospf nei

    Neighbor ID     Pri   State           Dead Time   Address         Interface
    192.168.245.2     1   FULL/DROTHER    00:00:38    192.168.245.2   GigabitEthernet0/0
    192.168.245.1     1   FULL/BDR        00:00:38    192.168.245.1   GigabitEthernet0/0
    R5#
    ```

    This fixed it.

??? "4. PC1 and PC2 can't ping the external server 8.8.8.8.  Why?  Fix the problem."

    R5 is connected to the internet. First, we notice that when we check the routing tables of the other routers, we don't see a default route. Lets check if R5 is advertising its default route.

    ``` bash
    R5#show running-config | section ospf
    router ospf 1
    log-adjacency-changes
    network 192.168.245.3 0.0.0.0 area 0
    default-information originate
    R5#
    ```

    The command is configured, so R5 should be sending this out but maybe R5 doesn't have a default route?

    ``` bash
    R5#show ip route
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
        D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
        N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
        E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
        i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
        * - candidate default, U - per-user static route, o - ODR
        P - periodic downloaded static route

    Gateway of last resort is not set

        10.0.0.0/24 is subnetted, 2 subnets
    O       10.0.1.0/24 [110/66] via 192.168.245.1, 00:04:05, GigabitEthernet0/0
    O       10.0.2.0/24 [110/3] via 192.168.245.2, 00:04:05, GigabitEthernet0/0
        192.168.12.0/30 is subnetted, 1 subnets
    O       192.168.12.0/30 [110/65] via 192.168.245.1, 00:04:05, GigabitEthernet0/0
        192.168.34.0/30 is subnetted, 1 subnets
    O       192.168.34.0/30 [110/2] via 192.168.245.2, 00:04:05, GigabitEthernet0/0
        192.168.245.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.245.0/29 is directly connected, GigabitEthernet0/0
    L       192.168.245.3/32 is directly connected, GigabitEthernet0/0
        203.0.113.0/24 is variably subnetted, 2 subnets, 2 masks
    C       203.0.113.0/30 is directly connected, GigabitEthernet0/0/0

    R5#
    ```

    It doesn't so lets create one

    ``` bash
    R5#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R5(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
    R5(config)#
    ```

    Now the PCs should be able to ping.

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  