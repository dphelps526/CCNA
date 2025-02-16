# Rapid STP

## Introduction

### Packet Tracer

[Download Day 22 Lab - Configuring Spanning Tree](/JITL/Day%2022%20Lab%20-%20Rapid%20STP.pkt){:download="Day 22 Lab - Rapid STP"}

### Topology

<figure markdown>
  ![Day 22 Topology](/PT_screenshots/day22.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Which switch is the root bridge? Use the CLI to examine the port role/state of each interface on the root. What appears different than what you have learned about the root bridge? What is the cause of this?
2. Without using the CLI, determine the port role/state of each remaining switch interface. Use the CLI to confirm.
3. Manually configure the appropriate RSTP link type on each interface. What do you think is the correct link type for SW1's F0/24?

## Answers

??? "1. Which switch is the root bridge? Use the CLI to examine the port role/state of each interface on the root. What appears different than what you have learned about the root bridge? What is the cause of this?"

    * `show spanning-tree`

    Each switch has a priority of 32769, which is 32768+1 so we can tell that they're all for VLAN 1.

    SW1 is the Root Bridge. In previous STP labs and lectures, we learned that every port on the root bridge is designated. In reality, it is more accurate to say that the root bridge has a designated port for each collision domain that it is connected to.

    <figure markdown>
        ![Day 22 Topology](/PT_screenshots/day22_A.PNG){ width="800" }
        <figcaption></figcaption>
    </figure>

??? "2. Without using the CLI, determine the port role/state of each remaining switch interface. Use the CLI to confirm."


    ??? abstract "Step 1: One Switch is elected as the Root Bridge. All ports on the Root Bridge are Designated Ports. (kind of)"

        Tie Breaker:
             
        - Lowerst Bridge ID
        - Lowest MAC Address

        First, we already know the root bridge.

        <figure markdown>
            ![Day 22 Topology](/PT_screenshots/day22_B.PNG){ width="800" }
            <figcaption></figcaption>
        </figure>

    ??? abstract "Step 2: Each remaining switch will select ONE of its interfaces to be its Root Port. Ports across Root Ports are always Designated Ports."

        Tie Breaker:
             
        - Lowest Root Cost
        - Lowest neighbor bridge ID
        - Lowest neighbor port ID

        Next, every other switch needs to have a Root Port. The first criteria to determine the root port is the root cost, that is, the interface with the lowest root cost will become the root port.

        Then, any port connected to a Root Port is also Designated. Therefore, SW4's G0/1 port becomes designated.

        <figure markdown>
            ![Day 20 Topology](/PT_screenshots/day22_C.PNG){ width="800" }
            <figcaption></figcaption>
        </figure>

    ??? abstract "Step 3: Each remaining collision domain will select one interface to be a Designated Port. The other port will be Non-Designated."

        Tie Breaker:
             
        - Interface on switch with lowest root cost
        - interface on switch with lowest bridge ID

        Next, in each remaining collision domain, we must determine which side will be Designated and which will be Non-Designated.
       
        <figure markdown>
            ![Day 20 Topology](/PT_screenshots/day22_D.PNG){ width="800" }
            <figcaption></figcaption>
        </figure>

    

??? "3. Manually configure the appropriate RSTP link type on each interface. What do you think is the correct link type for SW1's F0/24?"

    === "SW1"

        ``` bash
        SW1>en
        SW1#conf t

        SW1(config)#int f0/24
        SW1(config-if)#spanning-tree portfast 

        %Warning: portfast should only be enabled on ports connected to a single
        host. Connecting hubs, concentrators, switches, bridges, etc... to this
        interface  when portfast is enabled, can cause temporary bridging loops.
        Use with CAUTION

        %Portfast has been configured on FastEthernet0/24 but will only
        have effect when the interface is in a non-trunking mode.
        SW1(config-if)#
        ```

        ??? abstract "Confirm"

            IRL, the Type for F0/24 would show Shr and Edge. Shr will be there if it's full duplex. Edge means portfast is enabled

            ``` bash
            SW1(config-if)#do sh span

            VLAN0001
            Spanning tree enabled protocol rstp
            Root ID    Priority    32769
                        Address     0005.5E4E.714B
                        This bridge is the root
                        Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

            Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                        Address     0005.5E4E.714B
                        Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                        Aging Time  20

            Interface        Role Sts Cost      Prio.Nbr Type
            ---------------- ---- --- --------- -------- --------------------------------
            Fa0/1            Desg FWD 19        128.1    P2p
            Fa0/2            Desg FWD 19        128.2    Shr
            Fa0/3            Back BLK 19        128.3    Shr
            Fa0/24           Desg FWD 19        128.24   Shr

            SW1(config-if)#
            ```

    === "SW2"

        ``` bash
        SW2>en
        SW2#conf t
        SW2(config)#int range f0/23-24
        SW2(config-if-range)#spanning-tree portfast 

        %Warning: portfast should only be enabled on ports connected to a single
        host. Connecting hubs, concentrators, switches, bridges, etc... to this
        interface  when portfast is enabled, can cause temporary bridging loops.
        Use with CAUTION

        %Portfast has been configured on FastEthernet0/23 but will only
        have effect when the interface is in a non-trunking mode.
        %Warning: portfast should only be enabled on ports connected to a single
        host. Connecting hubs, concentrators, switches, bridges, etc... to this
        interface  when portfast is enabled, can cause temporary bridging loops.
        Use with CAUTION

        %Portfast has been configured on FastEthernet0/24 but will only
        have effect when the interface is in a non-trunking mode.
        SW2(config-if-range)#
        ```

    === "SW3"

        Note: If we ran 'sh span' first, we would see that f0/2 is already shared so technically the only one we need to do is set f0/24 as an edge port

        ``` bash
        SW3>en
        SW3#conf t

        ! Configuring F0/24 as an edge port by enabling portfast
        SW3(config)#int f0/24
        SW3(config-if)#spanning-tree portfast 

        %Warning: portfast should only be enabled on ports connected to a single
        host. Connecting hubs, concentrators, switches, bridges, etc... to this
        interface  when portfast is enabled, can cause temporary bridging loops.
        Use with CAUTION

        %Portfast has been configured on FastEthernet0/24 but will only
        have effect when the interface is in a non-trunking mode.


        ```

        ??? abstract "Confirm"

            We'll see that f0/2 is connected to a hub so we expect to see Shr

            ``` bash
            SW3(config-if)#do sh span
            VLAN0001
            Spanning tree enabled protocol rstp
            Root ID    Priority    32769
                        Address     0005.5E4E.714B
                        Cost        19
                        Port        2(FastEthernet0/2)
                        Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

            Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                        Address     000C.8519.6EBA
                        Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                        Aging Time  20

            Interface        Role Sts Cost      Prio.Nbr Type
            ---------------- ---- --- --------- -------- --------------------------------
            Fa0/2            Root FWD 19        128.2    Shr
            Fa0/1            Desg BLK 19        128.1    P2p
            Fa0/24           Desg BLK 19        128.24   P2p
            Gi0/1            Desg BLK 4         128.25   P2p

            SW3(config-if)#
            ```

    === "SW4"

        ``` bash
        SW4>en
        SW4#conf t

        ! Setting F0/1 and F0/2 as P2P because they are directly connected
        SW4(config)#int range f0/1-2
        SW4(config-if-range)#spanning-tree link-type point-to-point 

        ! Configuring F0/24 as an edge port by enabling portfast
        SW4(config-if-range)#int f0/24
        SW4(config-if)#spanning-tree portfast 

        %Warning: portfast should only be enabled on ports connected to a single
        host. Connecting hubs, concentrators, switches, bridges, etc... to this
        interface  when portfast is enabled, can cause temporary bridging loops.
        Use with CAUTION

        %Portfast has been configured on FastEthernet0/24 but will only
        have effect when the interface is in a non-trunking mode.
        ```

        ??? abstract "Confirm"

            We'll see that 1 and 2 are set to P2P.

            IRL, the Type for F0/24 would show P2p and Edge. P2p will be there if it's full duplex. Edge means portfast is enabled

            ``` bash
            SW4(config-if)#do sh spanni

            VLAN0001
            Spanning tree enabled protocol rstp
            Root ID    Priority    32769
                        Address     0005.5E4E.714B
                        Cost        38
                        Port        1(FastEthernet0/1)
                        Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

            Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                        Address     00E0.A381.AD46
                        Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                        Aging Time  20

            Interface        Role Sts Cost      Prio.Nbr Type
            ---------------- ---- --- --------- -------- --------------------------------
            Fa0/2            Altn BLK 19        128.2    P2p
            Fa0/1            Root FWD 19        128.1    P2p
            Fa0/24           Desg FWD 19        128.24   P2p

            SW4(config-if)#
            ```


## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  