# NETWORK ADDRESSES

Created: April 3, 2023 5:36 PM
Reviewed: No

# **Learning Objectives**

By the end of this chapter, you should be able to:

- Differentiate between different types of IPv4 and IPv6 addresses
- Understand IP Address Classes
- Understand the role of netmasks
- Get, set, and change the hostname
- Use NTP to set, synchronize, and correct the time

# i**P Addresses**

IP addresses are used to uniquely identify nodes across the internet. They are registered through ISPs (Internet Service Providers).

The IP address is the number that identifies your system on the network. It comes in two varieties.\

**Types of IP Addresses**

- Close IPv4
    
    IPv4 is a 32-bit address, composed of 4 octets (an octet is just 8 bits, or a byte).
    
    Example: **148.114.252.10**
    
    IPv6
    
    IPv6 is a 128-bit address, composed of 8 16-bit octet pairs.
    
    Example: **2003:0db5:6123:0000:1f4f:0000:5529:fe23**
    
    In either case, a set of *reserved addresses* is also included. We will focus on IPv4, as it is still what is most commonly in use.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/auukqeo1acxu-IPaddresses.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/auukqeo1acxu-IPaddresses.png)
    
    **IP Addresses**
    
    ## ****IPv4 Address Types****
    
    Unicast
    
    An address associated with a specific host. It might be something like **140.211.169.4** or **64.254.248.193**.
    
    Network
    
    An address whose host portion is set to all binary zeroes. Ex. **192.168.1.0**. (the host portion can be the last 1-3 octets as discussed later; here it is just the last octet).
    
    Broadcast
    
    An address to which each member of a particular network will listen. It will have the host portion set to all 1 bits, such as in **172.16.255.255** or **148.114.255.255** or **192.168.1.255**. (the host portion is the last two octets in the first two cases, just the last one in the third case).
    
    Multticast
    
    An address to which appropriately configured nodes will listen. The address **224.0.0.2** is an example of a multicast address. Only nodes specifically configured to pay attention to a specific multicast address will interpret packets for that multicast group.
    
    # **Reserved Addresses**
    
    Certain addresses and address ranges are reserved for special purposes.
    
    **Examples of Reserved Addresses**
    
    127.x.x.x
    
    Reserved for the loopback (local system) interface, where **0 <= x <= 254**. Generally, **127.0.0.1**.
    
    0.0.0.0
    Used by systems that do not yet know their own address. Protocols like DHCP and BOOTP use this address when attempting to communicate with a server.
    
    255.255.255.255
    Generic broadcast private address, reserved for internal use. These addresses are never assigned or registered to anyone. They are generally not routable.
    
    Other reserved addresses
    
    Other examples of reserved address ranges include:
    
    - **10.0.0.0 - 10.255.255.255**
    - **172.16.0.0 - 172.31.255.255**
    - **192.168.0.0 - 192.168.255.255**
    - Etc.
    
    Each of these has a purpose. For example, the familiar address range, **192.168.x.x** is used only for local communications within a private network.
    
    ****IPv6 Address Types****
    
    **Unicast**
    
    A packet is delivered to one interface.
    
    - **Link-local**: Auto-configured for every interface to have one. Non-routable.
    - **Global**: Dynamically or manually assigned. Routable.
    - Reserved for documentation.
    - 
    
    **Multicast**
    A packet is delivered to multiple interfaces.
    
    **Anycast**
    A packet is delivered to the nearest of multiple interfaces (in terms of routing distance).
    
    **IPv4-Mapped**
    
    An IPv4 address mapped to IPv6. For example, **::FFFF:a.b.c.d/96**
    
    In addition, IPv6 has some special types of addresses such as loopback, which is assigned to the **lo** interface, as **::1/128**.
    
    # **IPv4 Address Classes**
    
    Historically, IP addresses are based on defined classes. Classes A, B, and C are used to distinguish a network portion of the address from a host portion of the address. This is used for routing purposes.
    
    Class A addresses use 8 bits for the network portion of the address and 24 bits for the host portion of the address. Class B addresses use 16 and 16 bits respectively, while Class C addresses use 24 bits for the network portion and 8 bits for the host portion.
    
    Class D addresses are used for multicasting. Class E addresses are currently not used.
    
    | NETWORK CLASS | HIGHEST ORDER OCTET RANGE | NOTES |
    | --- | --- | --- |
    | A | 1-127 | 128 networks, 16,772,214 hosts per network, 127.x.x.x reserved for loopback |
    | B | 128-191 | 16,384 networks, 65,534 hosts per network |
    | C | 192-223 | 2,097,152 networks, 254 hosts per network |
    | D | 224-239 | Multicast addresses |
    | E | 240-254 | Reserved address range |
    
    # **Netmasks**
    
    **netmask** is used to determine how much of the address is used for the network portion and how much for the host portion as we have seen. It is also used to determine network and broadcast addresses.
    
    - Class A addresses use 8 bits for the network portion of the address and 24 bits for the host portion of the address
    - Class B addresses use 16 bits for the network and 16 bits for the host
    - Class C addresses use 24 bits for the network and 8 bits for the host
    - Class D addresses are used for multicasting
    - Class E addresses are currently not used
    
    | NETWORK CLASS | DECIMAL | HEX | BINARY |
    | --- | --- | --- | --- |
    | A | 255.0.0.0 | ff:00:00:00 | 11111111 00000000 00000000 00000000 |
    | B | 255.255.0.0 | ff:ff:00:00 | 11111111 11111111 00000000 00000000 |
    | C | 255.255.255.0 | ff:ff:ff:00 | 11111111 11111111 11111111 00000000 |
    
    We can calculate the network address by **anding** (logical and - &) the IP address with the netmask. We are interested in the network addresses because they define a local network which consists of a collection of nodes connected via the same media and sharing the same network address. All nodes on the same network can directly see each other.
    
    Example:
    
    **172.16.2.17 ip address&255.255.0.0 netmask----------------- 172.16.0.0 network address**
    
    # **Getting and Setting the Hostname**
    
    The hostname is simply a label to distinguish a networked device from other nodes. Historically, this has also been called a *nodename*.
    
    For DNS purposes, hostnames are appended with a period (dot) and a domain name, so that a machine with a hostname of **antje** could have a fully qualified domain name (FQDN) of **antje.linuxfoundation.org**.
    
    The hostname is generally specified at installation time, but can be changed at any time later.
    
    Any user can get the hostname with (command and output below):
    
    **`$ hostnamewally`**
    
    Changing hostname requires root privilege (command and output below):
    
    **`$ sudo hostname lumpylumpy`**
    
    The current value is always stored in **/etc/hostname** on most Linux distributions. To do this persistently so changes survive a reboot use the **hostnamectl** command, part of the systemd infrastructure:
    
    **`$ sudo hostnamectl set-hostname lumpy`**
    
    Historically, making persistent changes involved changing configuration files in the **etc** directory tree. On Red Hat-based systems this was **/etc/sysconfig/network**, on Debian-based systems this was **/etc/hostname** and on SUSE-based systems it was **/etc/HOSTNAME**. However, one should use the **hostnamectl** command on modern systems:
    
    **`$ hostnamectl`**
    
    ```bash
    **Static hostname: c8         
    Icon name: computer-desktop           
    Chassis: desktop        
    Machine ID: ce0c82382a8a4c80bbd6931a917a2f1c           
    Boot ID: 94207b3fbd9b4891b9a94e21762a47cb  
    Operating System: Red Hat Enterprise Linux 8.2 (Ootpa) \           
    dracut-049-70.git20200228.el8 (Initramfs)            
    Kernel: Linux 5.11.6      
    Architecture: x86-64**
    ```
    
    To see a usage message, type the following command:
    
    **`$ hostnamectl --help`**
    
    ```bash
    hostnamectl [OPTIONS...] COMMAND ...
    
    Query or change system hostname
    
    -h --help              Show this help
       --version           Show package version
       --no-ask-password   Do not prompt for password
    -H --host=[USER@]HOST  Operate on remote host
    -M --machine=CONTAINER Operate on local container
       --transient         Only set transient hostname
       --static            Only set static hostname
       --pretty            Only set pretty hostname
    
    Commands:
      status               Show current hostname settings
      set-hostname NAME    Set system hostname
      set-icon-name NAME   Set icon name for host
      set-chassis NAME     Set chassis type for host
      set-deployment NAME  Set deployment environment for host
      set-location NAME    Set location for host
    ```
    
    # **Network Time Protocol**
    
    Many protocols require consistent, if not accurate time to function properly.
    
    The security of many encryption systems is highly dependent on proper time. Industries such as commodities or stock trading require highly accurate time, as a difference of only seconds can mean hundreds, if not thousands of dollars.
    
    The Network Time Protocol (NTP) is a method to update and synchronize system time. NTP consists of a daemon and a protocol. NTP time sources are divided up into ”strata”:
    
    - A strata 0 clock is a special purpose time device (Atomic clock, GPS radio, etc.)
    - A strata 1 server is any NTP server connected directly to a strata 0 source (over serial or the like)
    - A strata 2 server is any NTP server which references a strata 1 server using NTP
    - A strata 3 server is any NTP server which references a strata 2 server using NTP
    
    NTP may function as a client, server, or a peer.
    
    - client - acquires time from a server or a peer
    - server - provides time to a client
    - peers - synchronize time between other peers regardless of the defined servers
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/7fdeg1s2630b-ntp.jpg](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/7fdeg1s2630b-ntp.jpg)
    
    **Network Time Protocol**
    
    # **NTP Applications**
    
    There are several NTP applications; some of the most common are:
    
    - **[ntp](http://www.ntp.org/)** - the default
    - **[chrony](http://chrony.tuxfamily.org/)** - designed to work in a wide range of environments, including intermittent network connections and virtual machines
    - **systemd-timesyncd** - an ntp client only included in the systemd package
    
    Implementation of NTP services varies widely between distributions. The most common elements of the configuration are similar for most popular time synchronization applications. Here are the common locations for the **ntp** applications configuration files:
    
    - ntp - **/etc/ntp.conf**
    - chrony - **/etc/chrony.conf**
    - systemd-timesyncd - **/etc/systemd/timesyncd.conf**
    
    Consult the related man pages for configuration details.
    
    The various ntp applications may conflict with each other, so only have one ntp application active at a time.
    
    # **Configuring the ntpd Client**
    
    A good NTP server is only as good as its time source. The [NTP Pool Project](http://www.pool.ntp.org/en/) was created to alleviate the load that was crippling the small number of NTP servers.The **pool** directive is aware of round-robin DNS servers and allows a different IP address on each request. If the connection allows peers, the **pool** command will establish peers on its own.
    
    The classic server directive may be used if desired.
    
    To configure your NTP server to use NTP Pool, edit **/etc/ntp.conf** and add or edit the following settings:
    
    **`/etc/ntp.conf`**
    
    ```bash
    driftfile /var/lib/ntp/ntp.drift
    pool 0.pool.ntp.org
    pool 1.pool.ntp.org
    pool 2.pool.ntp.org
    pool 3.pool.ntp.org
    ```
    
    The **ntpdc -c peers** command can show the time differential between the local system and configured time servers. The **timedatectl** command is in many distributions and may be used to query and control the system time and date. **timedatectl** is part of the systemd landscape and supports additional options when used with **systemd-timesyncd**.
    
    **`# timedatectl`**
    
    ```bash
    Local time: Sun 2023-03-05 16:13:50 UTC
               Universal time: Sun 2023-03-05 16:13:50 UTC
                     RTC time: Sun 2023-03-05 16:13:50
                    Time zone: Etc/UTC (UTC, +0000)
    System clock synchronized: yes
                  NTP service: active
              RTC in local TZ: no
    ```
    
    # **Configuring the ntpd Server**
    
    The configuration elements for the ntp server are contained in the **/etc/ntp.conf** file. Here are some of the ntp server relevant items:
    
    - Declare the local machine to be a time reference
        - The **server** and **fudge** directives
    - Regulate who can query the time server with **ntpq** and **ntpdc** commands (see CVE-2013-5211)
        - The restrict directives
    - Declare which systems are ntp peers
        - The peers are listed in the **/etc/ntp.conf** file
    - Start NTP daemon
    
    ### **Access Control**
    
    Here is an example of access control entries in **/etc/ntp.conf**.
    
    ```bash
    /etc/ntp.conf: access control
    # Default policy prevents queries
    restrict default nopeer nomodify notrap noquery
    # Allow queries from a particular subnet
    restrict 123.123.x.0 mask 255.255.255.0 nopeer nomodify notrap
    # Allow queries from a particular host
    restrict 131.243.1.42 nopeer nomodify notrap noquery
    # Unrestrict localhost
    restrict 127.0.0.1
    ```
    
    ### **Peer Configuration**
    
    Here is an example of ntp peer configuration entries.
    
    ```bash
    /etc/ntp.conf: peer configuration
    peer 128.100.49.12
    peer 192.168.0.1
    ```
    
    ### **Declaring Self a Time Source**
    
    This is an example of the ntp declaring itself as an NTP server. The second line, the **fudge** entry, specifies this server is a stratum 10 server.
    
    ```bash
    /etc/ntp.conf: declare self a time source
    server 127.127.1.0
    fudge 127.127.1.0 stratum 10
    ```