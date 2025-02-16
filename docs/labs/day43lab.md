# FTP and TFTP

## Introduction

### Packet Tracer

[Download Day 43 Lab - FTP and TFTP](/JITL/Day%2043%20Lab%20-%20FTP%20_%20TFTP.pkt){:download="Day 43 Lab - FTP and TFTP}

### Topology

<figure markdown>
  ![Day 43 Topology](/PT_screenshots/day43.PNG){ width="800" }
  <figcaption></figcaption>
</figure>

### Questions

1. Configure the appropriate IP addresses on each device. Configure routing on the routers to allow full connectivity.

2. Use TFTP on R1 to retrieve the following file from SRV1: c2900-universalk9-mz.SPA.155-3.M4a.bin

3. Upgrade R1's OS and then delete the old file from flash.

4. Use FTP on R2 to retrieve the following file from SRV1: c2900-universalk9-mz.SPA.155-3.M4a.bin
    - (FTP username: jeremy, password: ccna)

5. Upgrade R2's OS and then delete the old file from flash.

## Answers


??? "1. Configure the appropriate IP addresses on each device. Configure routing on the routers to allow full connectivity."

    === "R1"

        ``` bash
        R1>en
        R1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R1(config)#int g0/1
        R1(config-if)#ip add 10.0.0.254 255.255.255.0
        R1(config-if)#no shut

        R1(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

        R1(config-if)#int g0/0
        R1(config-if)#ip add 192.168.12.1 255.255.255.252
        R1(config-if)#no shut

        R1(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        R1(config-if)#
        ```

    === "R2"

        ``` bash
        R2>en
        R2#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        R2(config)#int g0/0
        R2(config-if)#ip add 192.168.12.2 255.255.255.252
        R2(config-if)#no shut

        R2(config-if)#
        %LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

        %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

        R2(config-if)#exit
        R2(config)#ip route 10.0.0.0 255.255.255.0 192.168.12.1
        ```

    === "SRV1"

        ``` bash
        Ip Address: 10.0.0.1
        Subnet: 255.255.255.0
        Default GW: 10.0.0.254
        ```

??? "2. Use TFTP on R1 to retrieve the following file from SRV1: c2900-universalk9-mz.SPA.155-3.M4a.bin"

    First, we'll check the current version on R1

    ``` bash
    R1#show version
    Cisco IOS Software, C2900 Software (C2900-UNIVERSALK9-M), Version 15.1(4)M4, RELEASE SOFTWARE (fc2)
    Technical Support: http://www.cisco.com/techsupport
    Copyright (c) 1986-2012 by Cisco Systems, Inc.
    Compiled Thurs 5-Jan-12 15:41 by pt_team

    ROM: System Bootstrap, Version 15.1(4)M4, RELEASE SOFTWARE (fc1)
    cisco2911 uptime is 11 minutes, 38 seconds
    System returned to ROM by power-on
    System image file is "flash0:c2900-universalk9-mz.SPA.151-1.M4.bin"
    Last reload type: Normal Reload
    ```

    The first line shows V15.1. We're about to download 15.5 (155). Lets also show the current version in the flash:

    ``` bash
    R1#show flash

    System flash directory:
    File  Length   Name/status
    3   33591768 c2900-universalk9-mz.SPA.151-4.M4.bin
    2   28282    sigdef-category.xml
    1   227537   sigdef-default.xml
    [33847587 bytes used, 221896413 available, 255744000 total]
    249856K bytes of processor board System flash (Read/Write)


    R1#
    ```

    Now, we'll grab the target file and put it into flash:

    ``` bash
    R1#copy tftp flash
    Address or name of remote host []? 10.0.0.1
    Source filename []? c2900-universalk9-mz.SPA.155-3.M4a.bin
    Destination filename [c2900-universalk9-mz.SPA.155-3.M4a.bin]? 

    Accessing tftp://10.0.0.1/c2900-universalk9-mz.SPA.155-3.M4a.bin...
    Loading c2900-universalk9-mz.SPA.155-3.M4a.bin from 10.0.0.1: !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
    [OK - 33591768 bytes]

    33591768 bytes copied in 0.341 secs (10343099 bytes/sec)
    R1#
    ```

??? "3. Upgrade R1's OS and then delete the old file from flash."

    Now we'll configure IOS to boot with this new version

    ``` bash
    R1#
    R1#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R1(config)#boot system flash c2900-universalk9-mz.SPA.155-3.M4a.bin


    R1(config)#end
    R1#
    %SYS-5-CONFIG_I: Configured from console by console

    R1#wr
    Building configuration...
    [OK]
    R1#reload
    Proceed with reload? [confirm]
    System Bootstrap, Version 15.1(4)M4, RELEASE SOFTWARE (fc1)
    Technical Support: http://www.cisco.com/techsupport
    Copyright (c) 2010 by cisco Systems, Inc.
    Total memory size = 512 MB - On-board = 512 MB, DIMM0 = 0 MB
    CISCO2911/K9 platform with 524288 Kbytes of main memory
    Main memory is configured to 72/-1(On-board/DIMM0) bit mode with ECC disabled

    Readonly ROMMON initialized

    program load complete, entry point: 0x80803000, size: 0x1b340
    program load complete, entry point: 0x80803000, size: 0x1b340

    IOS Image Load Test
    ___________________
    Digitally Signed Release Software
    program load complete, entry point: 0x81000000, size: 0x3bcd3d8
    Self decompressing the image :
    ########################################################################## [OK]
    Smart Init is enabled
    smart init is sizing iomem
                    TYPE      MEMORY_REQ
        Onboard devices &
            buffer pools      0x022F6000
    -----------------------------------------------
                    TOTAL:      0x022F6000
    Rounded IOMEM up to: 36Mb.
    Using 6 percent iomem. [36Mb/512Mb]

                Restricted Rights Legend
    Use, duplication, or disclosure by the Government is
    subject to restrictions as set forth in subparagraph
    (c) of the Commercial Computer Software - Restricted
    Rights clause at FAR sec. 52.227-19 and subparagraph
    (c) (1) (ii) of the Rights in Technical Data and Computer
    Software clause at DFARS sec. 252.227-7013.
            cisco Systems, Inc.
            170 West Tasman Drive
            San Jose, California 95134-1706

    Cisco IOS Software, C2900 Software (C2900-UNIVERSALK9-M), Version 15.5(3)M4a, RELEASE SOFTWARE (fc1)
    Technical Support: http://www.cisco.com/techsupport
    Copyright (c) 1986-2016 by Cisco Systems, Inc.
    Compiled Thu 06-Oct-16 14:43 by mnguyen
    ```

    Good practice will be to delete the old version

    ``` bash
    R1#delete flash:c2900-universalk9-mz.SPA.151-4.M4.bin
    Delete filename [c2900-universalk9-mz.SPA.151-4.M4.bin]?
    Delete flash:/c2900-universalk9-mz.SPA.151-4.M4.bin? [confirm]

    R1#show flash

    System flash directory:
    File  Length   Name/status
    4   33591768 c2900-universalk9-mz.SPA.155-3.M4a.bin
    2   28282    sigdef-category.xml
    1   227537   sigdef-default.xml
    [33847587 bytes used, 221896413 available, 255744000 total]
    249856K bytes of processor board System flash (Read/Write)


    R1#
    ```


??? "4. Use FTP on R2 to retrieve the following file from SRV1: c2900-universalk9-mz.SPA.155-3.M4a.bin"

    - (FTP username: jeremy, password: ccna)

    ``` bash
    R2#conf t
    R2(config)#ip ftp username jeremy
    R2(config)#ip ftp password ccna
    R2(config)#
    R2(config)#end
    R2#
    %SYS-5-CONFIG_I: Configured from console by console

    R2#copy ftp flash
    Address or name of remote host []? 10.0.0.1
    Source filename []? c2900-universalk9-mz.SPA.155-3.M4a.bin
    Destination filename [c2900-universalk9-mz.SPA.155-3.M4a.bin]? 

    Accessing ftp://10.0.0.1/c2900-universalk9-mz.SPA.155-3.M4a.bin...
    ```

??? "5. Upgrade R2's OS and then delete the old file from flash."

    ``` bash
    R2#conf t
    Enter configuration commands, one per line.  End with CNTL/Z.
    R2(config)#boot system flash c2900-universalk9-mz.SPA.155-3.M4a.bin
    R2(config)#exit
    R2#
    %SYS-5-CONFIG_I: Configured from console by console
    wr
    Building configuration...
    [OK]
    R2#reload
    Proceed with reload? [confirm]
    System Bootstrap, Version 15.1(4)M4, RELEASE SOFTWARE (fc1)
    ```

    Delete old version

    ``` bash
    R2#delete ?
    WORD    File to be deleted
    flash:  File to be deleted
    R2#delete flash:c2900-universalk9-mz.SPA.151-4.M4.bin
    Delete filename [c2900-universalk9-mz.SPA.151-4.M4.bin]?
    Delete flash:/c2900-universalk9-mz.SPA.151-4.M4.bin? [confirm]

    R2#
    ```

## Commands

* `spanning-tree portfast `
* `spanning-tree link-type point-to-point `

  