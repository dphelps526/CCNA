# Syslog

## Introduction

### Packet Tracer

[Download Day 41 Lab - Syslog](/JITL/Day%2041%20Lab%20-%20Syslog.pkt){:download="Day 41 Lab - Syslog}

### Topology

<figure markdown>
  ![Day 41 Topology](/PT_screenshots/day41.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

R1 username: jeremy, PW: ccna, enable PW: ccna

1. Connect to R1's console port using PC2:
    - Shut down the G0/0 interface
    - After you receive a syslog message, re-enable the interface.
    - What is the severity level of the syslog messages?
    - Enable timestamps for logging messages

2. Telnet from PC1 to R1's G0/0 interface (watch the video to learn how!)
    - Enable the unused G0/1 interface
    - Why does no syslog message appear?
    - Enable logging to the VTY lines for the current session.
    - there is no 'logging monitor' command in packet tracer, but it's enabled by default

3. Enable logging to the buffer, and configure the buffer size to 8192 bytes.

4. Enable logging to the syslog server SRV1 with a level of 'debugging'.

## Answers


??? "1. Connect to R1's console port using PC2:"

    - Shut down the G0/0 interface
    - After you receive a syslog message, re-enable the interface.
    - What is the severity level of the syslog messages?
    - Enable timestamps for logging messages

    ``` bash
    R1>en
    Password: 
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.

    R1(config)#int g0/0
    R1(config-if)#shut

    R1(config-if)#

    ! Two syslog messages, one for status and one for protocol
    %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to administratively down

    %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to down

    R1(config-if)#no shut

    R1(config-if)#
    %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

    %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

    R1(config-if)#exit
    R1(config)#service timestamps log datetime msec
    R1(config)#exit
    R1#

    ! Notice that we now have datetime
    *Feb 28, 10:05:26.055: SYS-5-CONFIG_I: Configured from console by console
    R1#
    ```


??? "2. Telnet from PC1 to R1's G0/0 interface (watch the video to learn how!)"

    - Enable the unused G0/1 interface
    - Why does no syslog message appear?
    - Enable logging to the VTY lines for the current session.
    - there is no 'logging monitor' command in packet tracer, but it's enabled by default

    ``` bash
    R1>en
    Password: 
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R1(config)#int g0/1
    R1(config-if)#no shut

    R1(config-if)#do terminal monitor
    R1(config-if)#shut

    R1(config-if)#
    *Feb 28, 17:46:29.4646: %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down
    ```

??? "3. Enable logging to the buffer, and configure the buffer size to 8192 bytes."

    ``` bash
    R1(config)#logging buffered 8192

    ```

    ??? abstract "Confirm"

        ``` bash
            R1(config)#do show logging 
        Syslog logging: enabled (0 messages dropped, 0 messages rate-limited,
                0 flushes, 0 overruns, xml disabled, filtering disabled)

        No Active Message Discriminator.


        No Inactive Message Discriminator.


            Console logging: level debugging, 9 messages logged, xml disabled,
                filtering disabled
            Monitor logging: level debugging, 2 messages logged, xml disabled,
                filtering disabled
            Buffer logging:  level debugging, 0 messages logged, xml disabled,
                filtering disabled

            Logging Exception size (4096 bytes)
            Count and timestamp logging messages: disabled
            Persistent logging: disabled

        No active filter modules.

        ESM: 0 messages dropped
            Trap logging: level informational, 9 message lines logged
        Log Buffer (8192 bytes):
        R1(config)#
        ```

??? "4. Enable logging to the syslog server SRV1 with a level of 'debugging'."

    ``` bash
    R1(config)#logging host 192.168.1.100
    R1(config)#logging trap debugging 
    ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  