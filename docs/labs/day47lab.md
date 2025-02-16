# QoS

## Introduction

### Packet Tracer

[Download Day 47 Lab - QoS](/docs/JITL/Day%2047%20Lab%20-%20QoS.pkt){:download="Day 47 Lab - QoS}

### Topology

<figure markdown>
  ![Day 47 Topology](/PT_screenshots/day47.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

Watch the video and follow along in the CLI to learn some basic QoS configurations. QoS configuration isn't a CCNA exam topic. 

Configure the following QoS settings on R1 and apply them outbound on interface G0/0/0:

1. Mark HTTPS traffic as AF31
    - Provide minimum 10% bandwidth as a priority queue

2. Mark HTTP traffic as AF32
    - Provide minimum 10% bandwidth

3. Mark ICMP traffic as CS2
    - Provide minimum 5% bandwidth

4. Use simulation mode to view the DSCP markings of packets:
    - when pinging jeremysitlab.com from PC1
    - when accessing jeremysitlab.com from PC1 via HTTP
    - when accessing jeremysitlab.com from PC1 via HTTPS
 

## Answers


??? "1. Mark HTTPS traffic as AF31"

    - Provide minimum 10% bandwidth as a priority queue

    ``` bash
    
    ```

??? "2. Mark HTTP traffic as AF32"

    - Provide minimum 10% bandwidth

    ``` bash

    ```
            
??? "3. Mark ICMP traffic as CS2"

    - Provide minimum 5% bandwidth

    ``` bash
    
    ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  