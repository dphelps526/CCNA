# Dyanmic NAT

## Introduction

### Packet Tracer

[Download Day 45 Lab - Dyanmic NAT](/docs/JITL/Day%2045%20Lab%20-%20Dynamic%20NAT.pkt){:download="Day 45 Lab - Dyanmic NAT}

### Topology

<figure markdown>
  ![Day 45 Topology](/PT_screenshots/day45.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Configure dynamic NAT on R1.
    - Configure the appropriate inside/outside interfaces
    - Translate all traffic from 172.16.0.0/24
    - Create a pool of 100.0.0.1 to 100.0.0.2 from the 100.0.0.0/24 subnet

2. Ping google.com from PC1 and PC2.  Then, ping it from PC3. What happens to PC3's ping?

3. Clear the NAT translations and remove the current NAT configuration. Switch the configuration to PAT using R1's public IP address.

4. Ping google.com from each PC.  Do the pings work? Examine the NAT translations on R1.

## Answers


??? "1. Configure dynamic NAT on R1."

    - Configure the appropriate inside/outside interfaces
    - Translate all traffic from 172.16.0.0/24
    - Create a pool of 100.0.0.1 to 100.0.0.2 from the 100.0.0.0/24 subnet

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
    R1(config)#access-list 1 permit 172.16.0.0 0.0.0.255
    R1(config)#ip nat pool POOL1 100.0.0.1 100.0.0.2 netmask 255.255.255.0
    R1(config)#ip nat inside source list 1 pool POOL1
    R1(config)#
    ```

??? "2. Ping google.com from PC1 and PC2.  Then, ping it from PC3. What happens to PC3's ping?"


??? "3. Clear the NAT translations and remove the current NAT configuration. Switch the configuration to PAT using R1's public IP address."

    ``` bash
    ! Clear translations
    R1#clear ip nat translation *
    R1#show ip nat translations 
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.

    ! Taking a look at what the current config is
    R1(config)#do show run | include nat
    ip nat outside
    ip nat inside
    ip nat pool POOL1 100.0.0.1 100.0.0.2 netmask 255.255.255.0
    ip nat inside source list 1 pool POOL1

    ! We don't need to start from scratch, we can just modify the statement and delete the pool
    R1(config)#no ip nat pool POOL1 100.0.0.1 100.0.0.2 netmask 255.255.255.0
    R1(config)#ip nat inside source list 1 int g0/0 overload
    R1(config)#
    ```

??? "4. Ping google.com from each PC.  Do the pings work? Examine the NAT translations on R1."

    ``` bash
    
    ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  