# Dynamic ARP Inspection

## Introduction

### Packet Tracer

[Download Day 51 Lab - Dynamic ARP Inspection](/JITL/Day%2051%20Lab%20-%20Dynamic%20ARP%20Inspection.pkt){:download="Day 51 Lab - Dynamic ARP Inspection}

### Topology

<figure markdown>
  ![Day 51 Topology](/PT_screenshots/day51.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Configure R1 as a DHCP server.
    - Exclude 192.168.1.1 - 192.168.1.9 from the pool
    - Default gateway: R1

2. Configure DHCP snooping on SW1 and SW2.

3. Configure DAI on SW1 and SW2.
    - Enable all additional validation checks
    - Trust ports connected to a router or switch

## Answers


??? "1. Configure R1 as a DHCP server."

    - Exclude 192.168.1.1 - 192.168.1.9 from the pool
    - Default gateway: R1

    ``` bash
    R1>
    R1>en
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.

    R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.9
    R1(config)#ip dhcp pool POOL1
    R1(dhcp-config)#network 192.168.1.0 255.255.255.0
    R1(dhcp-config)#default-router 192.168.1.1
    R1(dhcp-config)#
    ```

??? "2. Configure DHCP snooping on SW1 and SW2."

    === "SW1"

        ``` bash
        SW1>
        SW1>en
        SW1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.

        SW1(config)#ip dhcp snooping vlan 1

        SW1(config)#int g0/2
        SW1(config-if)#ip dhcp snooping trust

        SW2(config)#no ip dhcp snooping information option
        ```

    === "SW2"

        ``` bash
        SW2>
        SW2>en
        SW2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.

        SW2(config)#ip dhcp snooping vlan 1

        SW2(config)#int g0/1
        SW2(config-if)#ip dhcp snooping trust

        SW2(config)#no ip dhcp snooping information option
        ```
            
??? "3. Configure DAI on SW1 and SW2."

    - Enable all additional validation checks
    - Trust ports connected to a router or switch

    === "SW1"

        ``` bash
        SW1(config)#ip arp inspection vlan 1
        SW1(config)#ip arp inspection validate dst-mac ip src-mac 

        SW1(config-if)#int range g0/1-2
        SW1(config-if)#ip arp inspection trust
        ```

    === "SW2"

        ``` bash
        SW2(config)#ip arp inspection vlan 1
        SW2(config)#ip arp inspection validate dst-mac 
        SW2(config)#ip arp inspection validate ip
        SW2(config)#ip arp inspection validate src-mac 

        SW2(config)#int g0/1
        SW2(config-if)#ip arp inspection trust
        ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  