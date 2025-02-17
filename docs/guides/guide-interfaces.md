# Configuring Interfaces

## Configurations

??? Note "Basic Interface Configuration"

    ``` bash
    SW1#configure terminal 
    Enter configuration commands, one per line.  End with CNTL/Z.

    SW1(config)#interface fastEthernet 0/1
    SW1(config-if)#duplex full
    SW1(config-if)speed 100
    SW1(config-if)#description Printer on 2nd floor

    ! Default configuration
    SW1(config-if)no description
    SW1(config-if)#no speed
    SW1(config-if)#no duplex
    ```

    ??? abstract "Verify"

        ``` bash
        SW1#show interfaces status
        Port      Name               Status       Vlan       Duplex  Speed Type
        Fa0/1     Printer on 2nd flo notconnect   1          a-full  a-100 10/100BaseTX
        Fa0/2                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/3                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/4                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/5                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/6                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/7                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/8                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/9                        notconnect   1          auto    auto  10/100BaseTX
        Fa0/10                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/11                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/12                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/13                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/14                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/15                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/16                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/17                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/18                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/19                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/20                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/21                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/22                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/23                       notconnect   1          auto    auto  10/100BaseTX
        Fa0/24                       notconnect   1          auto    auto  10/100BaseTX
        Gig0/1                       notconnect   1          auto    auto  10/100BaseTX
        Gig0/2                       notconnect   1          auto    auto  10/100BaseTX

        SW1#
        ```


  