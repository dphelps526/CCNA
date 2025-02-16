# Port Security

## Introduction

### Packet Tracer

[Download Day 49 Lab - Port Security](/JITL/Day%2049%20Lab%20-%20Port%20Security.pkt){:download="Day 49 Lab - Port Security}

### Topology

<figure markdown>
  ![Day 49 Topology](/PT_screenshots/day49.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Configure port security on the following interfaces:
    - SW1 F0/1, F0/2, F0/3
        - Violation mode: Shutdown
        - Maximum addresses: 1
        - Sticky learning: Disabled
        - Aging time: 1 hour

    - SW2 G0/1
        - Violation mode: Restrict
        - Maximum addresses: 4
        - Sticky learning: Enabled

2. Trigger port security violations on SW1 and SW2 (for example by 
    connecting another PC) and observe the actions taken by each switch.

## Answers


??? "1. Configure port security on the following interfaces:"

    - SW1 F0/1, F0/2, F0/3
        - Violation mode: Shutdown
        - Maximum addresses: 1
        - Sticky learning: Disabled
        - Aging time: 1 hour

    - SW2 G0/1
        - Violation mode: Restrict
        - Maximum addresses: 4
        - Sticky learning: Enabled

    === "SW1"

        Everything is already a default except for the aging timer.

        ``` bash
        SW1>en
        SW1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        SW1(config)#int range f0/1-3
        SW1(config-if-range)#switchport port-security aging time 60

        SW1(config-if-range)#switchport port-security 
        Command rejected: FastEthernet0/1 is a dynamic port.
        Command rejected: FastEthernet0/2 is a dynamic port.
        Command rejected: FastEthernet0/3 is a dynamic port.
        SW1(config-if-range)#do show int f0/1
        ```
        
        This last command failed because the port is in dynamic auto, seen here:

        ``` bash
        SW1(config-if-range)#do show int f0/1 sw
        Name: Fa0/1
        Switchport: Enabled
        Administrative Mode: dynamic auto
        Operational Mode: static access
        Administrative Trunking Encapsulation: dot1q
        Operational Trunking Encapsulation: native
        Negotiation of Trunking: On
        Access Mode VLAN: 1 (default)
        Trunking Native Mode VLAN: 1 (default)
        Voice VLAN: none
        Administrative private-vlan host-association: none
        Administrative private-vlan mapping: none
        Administrative private-vlan trunk native VLAN: none
        Administrative private-vlan trunk encapsulation: dot1q
        Administrative private-vlan trunk normal VLANs: none
        Administrative private-vlan trunk private VLANs: none
        Operational private-vlan: none
        Trunking VLANs Enabled: All
        Pruning VLANs Enabled: 2-1001
        Capture Mode Disabled
        Capture VLANs Allowed: ALL
        Protected: false
        Unknown unicast blocked: disabled
        Unknown multicast blocked: disabled
        Appliance trust: none


        SW1(config-if-range)#
        ```

        To enable port security, the interface must be manually configured as an access port or trunk port. Then we can try again

        ``` bash
        SW1(config-if-range)#switchport mode access
        SW1(config-if-range)#switchport port-security 
        ```

        ??? abstract "Confirm"

            ``` bash
            SW1#show port-sec int f0/1
            Port Security              : Enabled
            Port Status                : Secure-up
            Violation Mode             : Shutdown
            Aging Time                 : 60 mins
            Aging Type                 : Absolute
            SecureStatic Address Aging : Disabled
            Maximum MAC Addresses      : 1
            Total MAC Addresses        : 0
            Configured MAC Addresses   : 0
            Sticky MAC Addresses       : 0
            Last Source Address:Vlan   : 0000.0000.0000:0
            Security Violation Count   : 0

            SW1#
            ```


    === "SW2"

        ``` bash
        SW2>en
        SW2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.

        SW2(config)#int g0/1
        SW2(config-if)#switchport port-security violation restrict
        SW2(config-if)#switchport port-security maximum 4
        SW2(config-if)#switchport port-security mac-address sticky
        SW2(config-if)#switchport mode access

        SW2(config-if)#switchport port-security 
        ```

        ??? abstract "Confirm"

            ``` bash
            SW2(config-if)#do show port-sec int g0/1

            Port Security              : Enabled
            Port Status                : Secure-up
            Violation Mode             : Restrict
            Aging Time                 : 0 mins
            Aging Type                 : Absolute
            SecureStatic Address Aging : Disabled
            Maximum MAC Addresses      : 4
            Total MAC Addresses        : 1
            Configured MAC Addresses   : 0
            Sticky MAC Addresses       : 1
            Last Source Address:Vlan   : 0060.471C.1D19:1
            Security Violation Count   : 0

            SW2(config-if)#
            ```

            Also, after pinging R1 from all three PCs, we can run this command again and also look at G0/1 in the running-config

            ``` bash
            SW2(config-if)#do show port-sec int g0/1
            Port Security              : Enabled
            Port Status                : Secure-up
            Violation Mode             : Restrict
            Aging Time                 : 0 mins
            Aging Type                 : Absolute
            SecureStatic Address Aging : Disabled
            Maximum MAC Addresses      : 4
            Total MAC Addresses        : 4
            Configured MAC Addresses   : 0
            Sticky MAC Addresses       : 4
            Last Source Address:Vlan   : 0003.0003.0003:1
            Security Violation Count   : 0

            SW2(config-if)#
            ```

            ``` bash
            interface FastEthernet0/23
            !
            interface FastEthernet0/24
            !
            interface GigabitEthernet0/1
            switchport mode access
            switchport port-security
            switchport port-security maximum 4
            switchport port-security mac-address sticky 
            switchport port-security violation restrict 
            switchport port-security mac-address sticky 0001.0001.0001
            switchport port-security mac-address sticky 0002.0002.0002
            switchport port-security mac-address sticky 0003.0003.0003
            switchport port-security mac-address sticky 0060.471C.1D19
            !
            interface GigabitEthernet0/2
            !
            interface Vlan1
            no ip address
            shutdown
            ```

            ``` bash
            SW2#show mac-address-table 
                    Mac Address Table
            -------------------------------------------

            Vlan    Mac Address       Type        Ports
            ----    -----------       --------    -----

            1    0001.0001.0001    STATIC      Gig0/1
            1    0002.0002.0002    STATIC      Gig0/1
            1    0003.0003.0003    STATIC      Gig0/1
            1    0060.471c.1d19    STATIC      Gig0/1
            1    00e0.b0d8.7e01    DYNAMIC     Gig0/2
            SW2#
            ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  