# IPv6 Configuration (Part 1)

## Introduction

### Packet Tracer

[Download Day 31 Lab - IPv6 Configuration (Part 1)](/JITL/Day%2031%20Lab%20-%20IPv6%20Configuration%20(Part%201).pkt){:download="Day 31 Lab - IPv6 Configuration (Part 1)}

### Topology

<figure markdown>
  ![Day 31 Topology](/PT_screenshots/day31.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

The IPv4 configuration of each device is complete.
Perform the following IPv6 configurations to create an 
IPv4/IPv6 'dual-stack' network.

1. Enable IPv6 routing on R1.

2. Configure the appropriate IPv6 addresses on R1.

3. Confirm your configurations. What IPv6 addresses are present on each interface?

4. Configure the appropriate IPv6 addresses on each PC. Configure the correct default gateway.

5. Attempt to ping between the PCs (IPv4 and IPv6)

## Answers


??? "1. Enable IPv6 routing on R1."

    ``` bash
    R1(config)#ipv6 unicast-routing 
    ```


??? "2. Configure the appropriate IPv6 addresses on R1."

    ``` bash
    R1>
    R1>en
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R1(config)#int g0/0
    R1(config-if)#ipv6 add 2001:db8:0:1::1/64
    R1(config-if)#int g0/1
    R1(config-if)#ipv6 add 2001:db8:0:2::/64
    R1(config-if)#int g0/2
    R1(config-if)#ipv6 add 2001:db8:0:3::1/64
    R1(config-if)#
    ```
??? "3. Confirm your configurations. What IPv6 addresses are present on each interface?"

    ``` bash
    R1(config-if)#do show ipv6 int bri
    GigabitEthernet0/0         [up/up]
        FE80::201:97FF:FE9A:AC01
        2001:DB8:0:1::1
    GigabitEthernet0/1         [up/up]
        FE80::201:97FF:FE9A:AC02
        2001:DB8:0:2::1
    GigabitEthernet0/2         [up/up]
        FE80::201:97FF:FE9A:AC03
        2001:DB8:0:3::1
    Vlan1                      [administratively down/down]
        unassigned
    R1(config-if)#
    ```

??? "4. Configure the appropriate IPv6 addresses on each PC. Configure the correct default gateway."

    === "PC1"

        ``` bash
        Default GW: 2001:db8:0:1::1
        IP Add: 2001:db8:0:1::2
        Subnet: 64
        ```

    === "PC2"

        ``` bash
        Default GW: 2001:db8:0:2::1
        IP Add: 2001:db8:0:2::2
        Subnet: 64
        ```

    === "PC3"

        ``` bash
        Default GW: 2001:db8:0:3::1
        IP Add: 2001:db8:0:3::2
        Subnet: 64
        ```

## Commands

* `ipv6 unicast-routing `

  