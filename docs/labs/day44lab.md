# Static NAT

## Introduction

### Packet Tracer

[Download Day 44 Lab - Static NAT](/JITL/Day%2044%20Lab%20-%20Static%20NAT.pkt){:download="Day 44 Lab - Static NAT}

### Topology

<figure markdown>
  ![Day 44 Topology](/PT_screenshots/day44.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Attempt to ping from PC1 to 8.8.8.8.  Does the ping work?

2. Configure static NAT on R1.
   - Configure the appropriate inside/outside interfaces
   - Map the IP addresses of PC1, PC2, and PC3 to 100.0.0.x/24

3. Ping 8.8.8.8 from PC1 again.  Does the ping work?

4. Ping google.com from each PC, and then check the NAT translations on R1.

5. Clear the NAT translations on R1.  Which entries remain?

## Answers


??? "1. Attempt to ping from PC1 to 8.8.8.8.  Does the ping work?"

    No

??? "2. Configure static NAT on R1."

    - Configure the appropriate inside/outside interfaces
    - Map the IP addresses of PC1, PC2, and PC3 to 100.0.0.x/24

    ``` bash
    R1>
    R1>en
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.

    R1(config)#int g0/0
    R1(config-if)#ip nat outside
    R1(config-if)#int g0/1
    R1(config-if)#ip nat inside
    R1(config-if)#exit
    R1(config)#
    R1(config)#

    R1(config)#ip nat inside source static 172.16.0.1 100.0.0.1
    R1(config)#ip nat inside source static 172.16.0.2 100.0.0.2
    R1(config)#ip nat inside source static 172.16.0.3 100.0.0.3
    ```
    

??? "3. Ping 8.8.8.8 from PC1 again.  Does the ping work?"

    Yes

??? "4. Ping google.com from each PC, and then check the NAT translations on R1."

    ``` bash
    R1#show ip nat translations
    Pro  Inside global     Inside local       Outside local      Outside global
    ---  100.0.0.1         172.16.0.1         ---                ---
    ---  100.0.0.2         172.16.0.2         ---                ---
    ---  100.0.0.3         172.16.0.3         ---                ---

    R1#
    ```


??? "5. Clear the NAT translations on R1.  Which entries remain?"

    ``` bash
    R1#clear ip nat translation ?
    *  Deletes all dynamic translations
    R1#clear ip nat translation *
    R1#
    R1#show ip nat trans
    R1#show ip nat translations 
    Pro  Inside global     Inside local       Outside local      Outside global
    ---  100.0.0.1         172.16.0.1         ---                ---
    ---  100.0.0.2         172.16.0.2         ---                ---
    ---  100.0.0.3         172.16.0.3         ---                ---

    R1#
    ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  