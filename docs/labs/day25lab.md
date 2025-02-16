# EIGRP

## Introduction

### Packet Tracer

[Download Day 25 Lab - EIGRP Configuration](/JITL/Day%2025%20Lab%20-%20EIGRP%20Configuration.pkt){:download="Day 25 Lab - EIGRP Configuration}

### Topology

<figure markdown>
  ![Day 25 Topology](/PT_screenshots/day25.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Configure the appropriate hostnames and IP addresses on each device.  Enable router interfaces.
2. Configure a loopback interface on each router (1.1.1.1/32 for R1, 2.2.2.2/32 for R2, etc.)
3. Configure EIGRP on each router.
    Disable auto-summary.
    Enable EIGRP on each interface (including loopback interfaces).
    Configure passive interfaces where appropriate (including loopback interfaces).
4. Configure R1 to perform unequal-cost load-balancing when sending network traffic to 192.168.4.0/24

## Answers


??? "1. Configure the appropriate hostnames and IP addresses on each device.  Enable router interfaces."

    === "R1"

        ``` bash
        Router>
        Router>en
        Router#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R1
        R1(config)#
        R1(config)#int g0/0
        R1(config-if)#ip add 10.0.12.1 255.255.255.252
        R1(config-if)#no shut

        R1(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        R1(config-if)#int f1/0
        R1(config-if)#ip add 10.0.13.1 255.255.255.252
        R1(config-if)#no shut

        R1(config-if)#
        %LINK-5-CHANGED: Interface FastEthernet1/0, changed state to up

        R1(config-if)#
        ```

    === "R2"

        ``` bash
        Router>en
        Router#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R2
        R2(config)#int g0/0
        R2(config-if)#ip add 10.0.12.2 255.255.255.252
        R2(config-if)#no shut

        R2(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

        R2(config)#int f1/0
        R2(config-if)#ip add 10.0.24.1 255.255.255.252
        R2(config-if)#no shut

        R2(config-if)#
        %LINK-5-CHANGED: Interface FastEthernet1/0, changed state to up

        R2(config-if)#
        ```

    === "R3"

        ``` bash
        Router>en
        Router#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R3
        R3(config)#int f1/0
        R3(config-if)#ip add 10.0.13.2 255.255.255.252
        R3(config-if)#no shut

        R3(config-if)#
        %LINK-5-CHANGED: Interface FastEthernet1/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up

        R3(config-if)#int f2/0
        R3(config-if)#ip add 10.0.34.1 255.255.255.252
        R3(config-if)#no shut

        R3(config-if)#
        %LINK-5-CHANGED: Interface FastEthernet2/0, changed state to up

        R3(config-if)#
        ```
    
    === "R4"

        ``` bash
        Router>
        Router>en
        Router# conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Router(config)#hostname R4
        R4(config)#int f1/0
        R4(config-if)#ip add 10.0.24.2 255.255.255.252
        R4(config-if)#no shut

        R4(config-if)#
        %LINK-5-CHANGED: Interface FastEthernet1/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet1/0, changed state to up

        R4(config-if)#int f2/0
        R4(config-if)#ip add 10.0.34.2 255.255.255.252
        R4(config-if)#no shut

        R4(config-if)#
        %LINK-5-CHANGED: Interface FastEthernet2/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet2/0, changed state to up

        R4(config-if)#
        R4(config-if)#int g0/0
        R4(config-if)#ip add 192.168.4.254 255.255.255.0
        R4(config-if)#no shut

        R4(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

        R4(config-if)#
        ```

    === "SW1"

        ``` bash
        Switch>
        Switch>en
        Switch#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        Switch(config)#hostname SW1
        SW1(config)#
        ```

    === "PC1"

        ``` bash
        Default GW: 192.168.4.254
        IP Add: 192.168.4.1
        Subnet: 255.255.255.0
        ```


??? "2. Configure a loopback interface on each router (1.1.1.1/32 for R1, 2.2.2.2/32 for R2, etc.)"

    === "R1"

        ``` bash
        R1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R1(config)#int loopback 0

        R1(config-if)#
        %LINK-5-CHANGED: Interface Loopback0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback0, changed state to up

        R1(config-if)#ip add 1.1.1.1 255.255.255.255
        R1(config-if)#
        ```

        ??? abstract "Confirm"

            ``` bash
            R1#
            %SYS-5-CONFIG_I: Configured from console by console
            sh ip int bri
            Interface              IP-Address      OK? Method Status                Protocol 
            GigabitEthernet0/0     10.0.12.1       YES manual up                    up 
            FastEthernet1/0        10.0.13.1       YES manual up                    up 
            FastEthernet2/0        unassigned      YES unset  administratively down down 
            Loopback0              1.1.1.1         YES manual up                    up
            R1#
            ```


??? "3. Configure EIGRP on each router."

    === "R1"

        ``` bash
        R1>en
        R1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.

        R1(config)#router eigrp 100
        R1(config-router)#net 10.0.12.0 0.0.0.3
        R1(config-router)#net 10.0.13.0 0.0.0.3
        R1(config-router)#net 1.1.1.1 0.0.0.0

        R1(config-router)#no auto-summary 
        R1(config-router)#
        %DUAL-5-NBRCHANGE: IP-EIGRP 100: Neighbor 10.0.12.2 (GigabitEthernet0/0) resync: summary configured

        %DUAL-5-NBRCHANGE: IP-EIGRP 100: Neighbor 10.0.13.2 (FastEthernet1/0) resync: summary configured

        R1(config-router)#passive-interface l0
        R1(config-router)#

        ```

    === "R2"

        ``` bash
        R2>
        R2>en
        R2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.

        R2(config)#router eigrp 100
        R2(config-router)#net 10.0.12.0 0.0.0.3
        R2(config-router)#net 10.0.24.0 0.0.0.3
        R2(config-router)#net 2.2.2.2 0.0.0.0

        R2(config-router)#no auto-summary 
        R2(config-router)#
        %DUAL-5-NBRCHANGE: IP-EIGRP 100: Neighbor 10.0.24.2 (FastEthernet1/0) resync: summary configured

        R2(config-router)#passive-interface l0
        R2(config-router)#
        ```

    === "R3"

        ``` bash
        R3>en
        R3#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.

        R3(config)#router eigrp 100
        R3(config-router)#net 10.0.13.0 0.0.0.3
        R3(config-router)#net 10.0.34.0 0.0.0.3
        R3(config-router)#net 3.3.3.3 0.0.0.0

        R3(config-router)#no auto-summary
        R3(config-router)#
        %DUAL-5-NBRCHANGE: IP-EIGRP 100: Neighbor 10.0.34.2 (FastEthernet2/0) resync: summary configured

        R3(config-router)#passive-interface l0

        R3(config-router)#
        ```

    === "R4"

        ??? abstract "Enable EIGRP on each interface"

            ``` bash
            R4>en
            R4#conf t
            Enter configuration commands, one per line.  End with CNTL/Z.

            ! AS number must match neighbor 
            R4(config)#router eigrp 100 

            ! Only do the 0.0.0.0 in lab, need to be more deliberate irl
            R4(config-router)# network 0.0.0.0 255.255.255.255
            ```

        ??? abstract "Disable Auto-Summary"

            ``` bash
            R4(config-router)#no auto-summary
            ```

            Then we can confirm that it's disabled by: 

            ``` bash
            R4(config)#do show ip protocol

            Routing Protocol is "eigrp  100 " 
            Outgoing update filter list for all interfaces is not set 
            Incoming update filter list for all interfaces is not set 
            Default networks flagged in outgoing updates  
            Default networks accepted from incoming updates 
            EIGRP metric weight K1=1, K2=0, K3=1, K4=0, K5=0
            EIGRP maximum hopcount 100
            EIGRP maximum metric variance 1
            Redistributing: eigrp 100
            Automatic network summarization is not in effect  
            Maximum path: 4
            Routing for Networks:  
                0.0.0.0
            Routing Information Sources:  
                Gateway         Distance      Last Update 
            Distance: internal 90 external 170

            R4(config)#
            ```

        ??? abstract "Configure passive interfaces where appropriate"

            ``` bash
            R4#
            R4#conf t
            Enter configuration commands, one per line.  End with CNTL/Z.

            R4(config)#router eigrp 100
            R4(config-router)#passive-interface g0/0
            R4(config-router)#passive-interface l0
            ```

            Then we can check if these addresses are listed 

            ``` bash
            R4(config-router)#do show ip prot

            Routing Protocol is "eigrp  100 " 
            Outgoing update filter list for all interfaces is not set 
            Incoming update filter list for all interfaces is not set 
            Default networks flagged in outgoing updates  
            Default networks accepted from incoming updates 
            EIGRP metric weight K1=1, K2=0, K3=1, K4=0, K5=0
            EIGRP maximum hopcount 100
            EIGRP maximum metric variance 1
            Redistributing: eigrp 100
            Automatic network summarization is not in effect  
            Maximum path: 4
            Routing for Networks:  
                0.0.0.0
            Passive Interface(s): 
                GigabitEthernet0/0
                Loopback0
            Routing Information Sources:  
                Gateway         Distance      Last Update 
            Distance: internal 90 external 170

            R4(config-router)#
            ```


## Commands

* `no auto-summary`
* `router eigrp 100`

  