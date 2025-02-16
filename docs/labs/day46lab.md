# Voice VLANs

## Introduction

### Packet Tracer

[Download Day 46 Lab - Voice VLANs](/docs/JITL/Day%2046%20Lab%20-%20Voice%20VLANs.pkt){:download="Day 46 Lab - Voice VLANs}

### Topology

<figure markdown>
  ![Day 46 Topology](/PT_screenshots/day46.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

**Telephony configurations (not relevant to the CCNA) have been pre-configured on R1**
1. Configure SW1's interfaces in the appropriate VLANs.

2. Configure ROAS for the connection between SW1 and R1.

3. In simulation mode, ping PC2 from PC1. Is the traffic tagged with a VLAN ID?

4. In simulation mode, call PH1 from PH2.  Is the traffic tagged with a VLAN ID?

## Answers


??? "1. Configure SW1's interfaces in the appropriate VLANs."

    ``` bash
    SW1>
    SW1>en
    SW1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.

    SW1(config)#int range g1/0/2-3

    SW1(config-if-range)#switchport access vlan 10
    % Access VLAN does not exist. Creating vlan 10

    SW1(config-if-range)#switchport voice vlan 20
    % Voice VLAN does not exist. Creating vlan 20

    ```

??? "2. Configure ROAS for the connection between SW1 and R1."

    We'll set up ROAS

    === "SW1"

        ``` bash
        SW1(config-if-range)#int g1/0/1
        SW1(config-if)#switchport mode trunk

        SW1(config-if)#switchport trunk allowed vlan 10,20
        SW1(config-if)#
        ```

    === "R1"

        ``` bash
        R1>
        R1>en
        R1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R1(config)#int f0/0
        R1(config-if)#no shut

        R1(config-if)#int f0/0.10
        R1(config-subif)#encapsulation dot1Q 10
        R1(config-subif)#ip add 192.168.10.1 255.255.255.0

        R1(config-subif)#int f0/0.20
        R1(config-subif)#encapsulation dot1Q 20
        R1(config-subif)#ip add 192.168.20.1 255.255.255.0
        ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  