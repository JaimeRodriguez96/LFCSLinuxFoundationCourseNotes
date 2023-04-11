# LINUX NETWORKING: CONCEPTS AND REVIEW

Created: April 10, 2023 2:53 PM
Status: Not started

# **Learning Objectives**

By the end of this section, you should be able to:

- Explain the OSI Model
- Outline various network topologies
- Explore the Domain Name Service (DNS) operation
- Start and stop system services.

# **The Open Systems Interconnection (OSI) Model**

The Open Systems Interconnection (OSI) model was created to standardize the language used to describe networking protocols. It defines the manner in which systems communicate with one another using abstraction layers. Each layer communicates with the layer directly above and below. Not all layers are used at all times.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/47s4lc4nv4i6-osi-stack.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/47s4lc4nv4i6-osi-stack.png)

**OSI Model**

| LAYER | NAME | DESCRIPTION |
| --- | --- | --- |
| 7 | Application | Most networking stacks have a concept of the application layer. |
| 6 | Presentation | Most networking stacks combine the presentation layer in layers 4, 5, or 7. |
| 5 | Session | Sometimes used by different stacks, often combined into other layers (7 or 6). |
| 4 | Transport | Most networking stacks have a concept of the transport layer. |
| 3 | Network | Most networking stacks have a concept of the network layer. |
| 2 | Data Link | Most networking stacks have a concept of the data link layer. |
| 1 | Physical | Most networking stacks have a concept of the physical layer. |

There are other models which are used to talk about networking. The most popular networking stack on the Internet today is the Internet Protocol Suite. The Internet Protocol Suite can be described using a subset of the OSI model. For more information on the Internet Protocol Suite, see: *["Requirements for Internet Hosts -- Communication Layers"](https://datatracker.ietf.org/doc/html/rfc1122)* and *["Requirements for Internet Hosts -- Application and Support"](https://datatracker.ietf.org/doc/html/rfc1123)*.

# **OSI Layer 1: Physical Layer**

**OSI Level 1** is also known as the **Physical layer**. This is where the ”signals” are converted to information the system can use. It deals with transferring bits over a physical medium such as:

- Electric pulses over copper cables
- Laser pulses over fiber optic cables
- Frequency modulations over radio waves
- Scraps of paper over carrier pigeons (to learn more see *["A Standard for the Transmission of IP Datagrams on Avian Carriers"](https://datatracker.ietf.org/doc/html/rfc1149)*)

The Physical layer is the lowest possible layer and deals with the actual physical transfer of information. There are various different protocols, hardware types and standards defined for different types of physical networks (commonly referred to as PHYs):

- IEEE 802.3: Copper or fiber connections (examples and additional information about various cables and connection is available on Wikipedia, in the *["10BASE2"](https://en.wikipedia.org/wiki/10BASE2)* article)
- IEEE 802.11: Wireless (Wi-Fi) connections
- Bluetooth: Wireless connections
- USB: Copper connections
- RS232: Copper serial connections

In the following graphic, an example of a **Frame** is expanded. A Frame is a unit of data collected from the layer 1 interface. We can see our example has collected 122 bytes in frame 3829 from adapter enp3s0:

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/6c2n7ftvm7sf-OSI-Layer-1a.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/6c2n7ftvm7sf-OSI-Layer-1a.png)

**Physical Layer**

# **OSI Layer 2: Data Link Layer**

The **Layer 2** is also known as the **Data Link** layer. Normally this layer accepts data from the hardware in Layer 1 and prepends an address to all the inbound packets it accepts. The address number is 6 bytes, 3 bytes for the manufacturer and 3 random bytes assigned to the adapter. Many analysis programs will show the name assigned by the factory.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/xlqbyw3yw9cp-OSI-Layer-2a.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/xlqbyw3yw9cp-OSI-Layer-2a.png)

**Data Link Layer**

In our example the factory names of D-Link and IntelCor are displayed.

This 6 byte address is known as the MAC (Media Access Control Address).

The Data Link layer deals with transferring data between network nodes using the MAC address. This layer is where the IP address and MAC address are associated using the ARP protocol. A broadcast is initiated to find the MAC address of the IP address that is required. Quite often a broadcast is initiated requesting MAC addresses as the associations time out and are deleted. The ARP frame usually has the data of Who has **192.168.0.2? Tell 192.168.0.7**.

Since ARP uses broadcasts, the packets are confined to a single network segment. A network segment may also be called a broadcast domain or collision domain. A collision domain may be expanded with the use of a bridge. Bridges will work but all nodes will hear and examine all traffic on the wire. Adding bridges increases the number of nodes on the wire.

Common Data Link protocols or EtherType include:

- ARP: ID=x0806, Address Resolution Protocol
- RARP: ID=x0825, Reverse Address Resolution Protocol
- IPv4: ID=x0800, Internet Protocol version 4
- PPPoE: ID=x8863, Point to Point Protocol over Ethernet (discovery)
- STP: Spanning Tree Protocol, does not

# **OSI Layer 3: Network Layer**

The **Network Layer 3** deals with routing and forwarding packets. Layer 3 may also have Quality of Service component.

The Data Link layer is confined to a single network segment and is used by Layer 3 for local delivery.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/jj5lzphs659j-OSI-Layer-3a.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/jj5lzphs659j-OSI-Layer-3a.png)

**Network Layer**

The associated graphic shows this is Internet Protocol version 4, the source and destination of the packet. This packet came from someplace else to this machine via the D-Link router.

Some of the common Layer 3 protocols are:

- IPv4, Internet Protocol version 4
- IPv6, Internet Protocol version 6
- OSPF, Open Shortest Path First
- IGRP, Interior Gateway Routing Protocol
- ICMP, Internet Control Message Protocol

Some of these will be looked at in greater detail.

The layer 3 is tasked with packet delivery to the next hop. Each packet is sent/forwarded on its own merits and run in a connectionless environment. Connection tracking may be implemented at a higher level.

Layer 3’s ability to forward packets based on the address forms the backbone of the Internet.

In many cases the final destination is not adjacent to this machine so the packets are routed based on the local routing table information.

# ****The Internet Protocol (IP)****

**The Internet Protocol Functions**

Originally the datagram service for TCP, the Internet Protocol now transfers many different higher level protocols. The Internet Protocol has two main functions.

Addressing
The addressing function examines the address on the incoming packet and decides if the datagram is for the local system or another system. If the address indicates the datagram is for the local system the headers are removed and the datagram is passed up to the next layer in the protocol stack. If the address indicates the datagram is for another machine then it is passed to the next system in the direction of the final destination.

Fragmentation
The fragmentation component will split and re-assemble the packets if the path to the next system uses a smaller transmission unit size.

# **More About IP: IP Routing**

The associated diagram illustrates the routing path and decisions made for routing packets. This illustration is IPv4 based, IPv6 is very similar with some additional features.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/z001sc7k07ri-OSI-Layer-3.1.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/z001sc7k07ri-OSI-Layer-3.1.png)

**OSI Layer 3 Routing**

Routing begins when the adapter collects a Frame of data and passes it up the stack.

Routing processing:

1. In the first stage the Data Link (MAC) is examined to see if it matches the local machine’s hardware address.
2. When a match is made, the packet is examined for a match of the Destination IP Address.
3. If a match of IP addresses is made, then the packet’s destination is the local machine. The packet is then passed up to Layer 4 (Transport), for additional processing.
4. If there is no IP address match then the datagram is forwarded to the next hop based on the local routing tables.
5. The MAC address is updated to the next hop machine and the packet is passed along. Notice that the MAC address is modified to the next hop and the IP address is the final destination and is not usually modified.

IP packets can only reach from end to end if they are routed properly.

Network routers inspect packet headers, and make routing decisions based on the destination address.

Most protocols are single streamed, that is per connection it is a single conversation. Many single streams can be created as required. The graphic below is illustrating a client with communication connections to several servers. There are multiple routes the packets can take to reach the final destination and the routers involved make routing decisions based on path data for most direct path or fastest path. Depending on requirements routing devices can be customized for the workload.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/lvlc0ehpj4js-Routing-eps-converted-to.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/lvlc0ehpj4js-Routing-eps-converted-to.png)

**IP Routing**

# **More About IP: IP Addressing**

There are two major versions of IP: IPv4 and IPv6.

**IPv4 and IPv6**

IPv4 example.com – 192.0.43.10

IPv4 was the first major Internet Protocol version:

- It is the most used protocol on the Internet
- The 32bit address size allows for 4,294,967,296 possible addresses
- The address space was exhausted on January 31, 2011
- The last allocation of blocks was given out on February 3, 2011.

The IPv4 address space exhaustion has been a concern for some time. There are different solutions for mitigating the problem:

- The move from Classed networks to Classless Inter-Domain Routing (CIDR)
- The invention of Network Address Translation (NAT)
- The move to IPv6.

- IPv6 example.com – 2001:500:88:200::10
    
    IPv6 is the successor to IPv4. It has a 128bit address size that allows for 3.4 x 1038 possible addresses. IPv6 was designed to deal with the exhaustion of IPv4 addresses and other IPv4 shortcomings:
    
    - Expanded addresses capabilities
    - Header format simplification
    - Improved support for extensions and options
    - Flow labeling capabilities.
    
    To learn more, please review the *["Internet Protocol, Version 6 (IPv6) Specification"](https://ietf.org/rfc/rfc2460.txt)* memo.
    
    # **More About IP: IP Address Parts**
    
    IP addresses have two parts:
    
    - The Network PartThe network component of the address is indicated by the value of the netmask.
    - The Host PartThe host component is the remaining bits after the host network component is removed.
    
    They are distinguished by using a **netmask**, a bitmask defining which part of an IP address is the network part.
    
    | IP Addr Dec | 10 | 20 | 0 | 100 |
    | --- | --- | --- | --- | --- |
    | Netmask Dec | 255 | 255 | 0 | 0 |
    | IP Addr Bin | 00001010 | 00010100 | 00000000 | 01100100 |
    | Netmask Bin | 11111111 | 11111111 | 00000000 | 00000000 |
    | Network Part Bin | 00001010 | 00010100 |  |  |
    | Host Part Bin |  |  | 00000000 | 01100100 |
    
    # **More About IP: IP Subnetting**
    
    IP networks can be broken into smaller pieces by using a subnet. The example below is breaking a network with a netmask of **255.255.0.0** into a smaller subnet with a netmask of **255.255.255.0**.
    
    | IP Addr Dec | 10 | 20 | 0 | 100 |
    | --- | --- | --- | --- | --- |
    | Netmask Dec | 255 | 255 | 0 | 0 |
    | IP Addr Bin | 00001010 | 00010100 | 00000000 | 01100100 |
    | Netmask Bin | 11111111 | 11111111 | 00000000 | 00000000 |
    | Subnet Bin |  |  | 11111111 |  |
    | Network Part Bin | 00001010 | 00010100 |  |  |
    | Subnet Part Bin |  |  | 00000000 |  |
    | Host Part Bin |  |  |  | 01100100 |
    
    # **More About IP: IP Network Classes**
    
    Originally, the IPv4 addresses were broken into the following three classes:
    
    - Class A: **0.0.0.0/255.0.0.0**
    - Class B: **128.0.0.0/255.255.0.0**
    - Class C: **192.0.0.0/255.255.255.0**
    
    Original classes of networks and subnets did not scale well. Networks which did not fit in a class B were often given a class A. This led to IP addresses going to waste and the creation of CIDR (Classless Inter-Domain Routing) which uses a numbered bitmask instead of the class bitmask.
    
    # **More About IP: Classless Inter-Domain Routing**
    
    There are two types of netmasks: Classed network netmasks and CIDR network netmasks.
    
    | Class A DecClass A Bin | 25511111111 | 000000000 | 000000000 | 000000000 |
    | --- | --- | --- | --- | --- |
    | Class B DecClass B Bin | 25511111111 | 2551111111111 | 000000000 | 000000000 |
    | Class C DecClass C Bin | 25511111111 | 2551111111111 | 2551111111111 | 000000000 |
    
    | CIDR / 18 DecCIDR / 18 Bin | 25511111111 | 25511111111 | 19211000000 | 000000000 |
    | --- | --- | --- | --- | --- |
    
    **Note:** CIDR network netmasks are more flexible, and they do not have to end on "nibble" boundaries.
    
    # **More About IP: IP Management Tools**
    
    ip is part of the **iproute2** package, the default tool for many distributions that manages layer 2 and 3 settings.
    
    **NetworkManager** is a daemon with D-bus for communication to applications and a robust API available to inspect network settings and operations. It has several interfaces:
    
    - cli: command line interface
    - tui: text user interface using curses text menus
    - gui: graphical user interface
    
    To set up an address with ip, run the following command:
    
    **`$ sudo ip addr add 10.20.0.100/16 dev eth0`**
    
    Next, we will provide some nmcli example commands.
    
    To display the adapter summary, run the following command:
    
    **`$ sudo nmcli`**
    
    To show connections and names, you can run:
    
    **`$ sudo nmcli con show`**
    
    The output for the above command can be seen below (you may get a slightly different result depending on your system setup):
    
    ```bash
    1 NAME                UUID                                  TYPE      DEVICE
    2 Wired connection 2  a348cfcd-5a9e-3daa-9e1f-f5a3d9744d82  ethernet  enp7s0
    3 ens33               1fc197ee-39aa-4721-b86a-682113867b6b  ethernet  --
    4 Wired connection 1  2fd5d5b7-260e-3150-b1cb-9419a231510a  ethernet  --
    ```
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/2gkzrtq9gaor-ifconfigpartofthenet-toolspackage.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/2gkzrtq9gaor-ifconfigpartofthenet-toolspackage.png)
    
    # **LAN: Local Area Network**
    
    Smaller network segments are sometimes referred to as collision domains since there can be only one transmitter on the physical medium at a time. This is common in hub-based networks. Modern switch technology removes the restriction of one at a time but the concept of broadcast domains still exists.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/1yrrt43dibhf-LAN.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/1yrrt43dibhf-LAN.png)
    
    **LAN: Local Area Network**
    
    - Smaller, locally connected network.
    - Connected at layer 2 by the same series of switches or hubs.
    - Node to node communication happens at layer 3 using the same network.
    - Layer 2 to layer 3 association may use layer 2 broadcasts and the ARP and RARP protocols to associate MAC addresses with IP addresses.
    
    # **Bridged Network: Hardware**
    
    A network bridge or repeater accepts packets on one side of the bridge and passes them through to the other side of the bridge. The effect is bi-directional.
    
    - It is a combination of two or more networks at layer 2.
    - Bridged networks communicate as a single network.
    - It is generally used to increase the length of the network connections or increase the number of connections by joining two LANs.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/i6dd2naeagwx-hw-bridge.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/i6dd2naeagwx-hw-bridge.png)
    
    **Hardware Bridged Network**
    
    # **Bridged Network: Software**
    
    Software bridges have been in the Kernel for a long time. They are usually only noticed when implementing VMs, containers or network namespaces.
    
    Often, DHCP services are associated with a bridge to assign addressing and routing to the "client" connecting to the bridge. There are several methods for configuring software bridges:
    
    - **iproute2**
    - **bridge-utils**– includes the **brctl** command– deprecated and may not be available on all distributions
    - **netctl** available on a limited number of distributions
    - **systemd-networkd**
    - **NetworkManager**– available on almost all distributions– has command line interface (**nmcli**), curses-based interface (**nmtui**) and graphical interface (**nm-connection-editor**)
    - Applications that create virtual environments like libvirt, lxc, qemu, VirtualBox, VMware, etc.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/8fih9tymd0vp-sw-bridge.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/8fih9tymd0vp-sw-bridge.png)
    
    **Software Bridged Network**
    
    # **WAN: Wide Area Network**
    
    WAN or Wide Area Networks are the components that make the internet work. Typically a WAN is comprised of many individual LAN’s connected together. In the graphic below three LANS are visible with five networks.
    
    - LAN_A has 3 nodes that belong to the 192.168.42.0 subnet.
    - LAN_B has 3 nodes that belong to the 192.168.41.0 subnet.
    - LAN_C has 3 nodes that belong to the 192.168.40.0 subnet.
    - Node_A2 belongs to two networks, 192.168.42.0 and 192.168.7.0.
    - Any node in LAN_A can pass packets to LAN_B via Node_B.
    - Nodes in LAN_B can send packets to LAN_A and LAN_C via nodes Node_B3 and Node_B1 respectively.
    - Connected at layer 3 (ip address) from node to node.
    - The layer 2 (MAC address) contains the address of the ”gateway” node.
    - Once the ”gateway” node receives the packet it determines if the packet is local or needs to be ”forwarded” to the next ”gateway”.
    - This process continues until the packet arrives at the designated layer 3 address.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/ikyygzinaabv-WAN-v2.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/ikyygzinaabv-WAN-v2.png)
    
    **WAN: Wide Area Network**
    
    # **VLAN: Virtual Local Area Network**
    
    Virtual Local Area Network (VLAN) is a method for combining two or more separated LANs to appear as the same LAN.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/gt5cyh3fw5ni-vlan-overview.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/gt5cyh3fw5ni-vlan-overview.png)
    
    **VLAN: Virtual Local Area Network**
    
    In the diagram:
    
    - VLAN 200 has members of U1,U6,U5
    - VLAN 142 has members of U2,U7
    - VLAN 100 has members of U1,U2,U3,U4
    
    VLAN is also a method for securing two or more LANs from each other on the same set of switches.
    
    Several configuration elements are usually required. Some of these can be minimized or eliminated with newer options and protocols:
    
    - Define the VLAN(s) to the switch.
    - Configure the trunk port for switch to switch communication and encapsulation.
    
    # **OSI Layer 4: Transport Layer**
    
    The **Transport Layer** is responsible for the end-to-end communication protocols. Data is properly multiplexed by defining source and destination port numbers. This layer also deals with reliability by adding check sums, doing request repeats, and avoiding congestion. It also deals with an end-to-end communication and handles reliability, flow control and multiplexing.
    
    Common protocols in the Transport Layer include:
    
    - Transmission Control Protocol (TCP)The main component of the TCP/IP (Internet Protocol Suite) stack.
    - User Datagram Protocol (UDP)Another popular component of the Internet Protocol Suite stack.
    - Stream Control Transmission Protocol (SCTP)
    
    Use port numbers to allow for connection multiplexing. The port numbers are usually used in pairs, servers have fixed ports that they listen on and clients use a random port number for port number.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/v99x0gf73bw4-OSI-Layer-4a.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/v99x0gf73bw4-OSI-Layer-4a.png)
    
    # **Transport Layer**
    
    Transport layer protocols use ports to distinguish between different types of traffic or to do multiplexing. The ports are classed in three different ways.
    
    ****Transport Layer Ports****
    
    Well-Known Ports (0-1023)
    They are assigned by the Internet Assigned Numbers Authority (IANA), and usually require super-user privilege to be bound. Some of the well-known ports are: 22 TCP: SSH; 25 TCP: SMTP; 80 TCP: HTTP; 443 TCP: HTTPS.
    
    **Registered Ports (1024-49151)**
    Registered ports are also assigned by the IANA. They can be bound on most systems by non-super-user privilege. Some of the registered ports are: 1194 TCP/UDP: OpenVPN; 1293 TCP/UDP: IPSec; 1433 TCP: MSSQL Server.
    
    **Dynamic or Ephemeral Ports (49152-65535)**
    The Ephemeral ports are used as source ports for the client-side of a Transmission Control Protocol (TCP) or User Datagram Protocol (UDP) connection. You can also use the Ephemeral ports for a temporary or non-root service.
    
    # **TCP vs UDP vs SCTP**
    
    TCP is useful when data integrity, ordered delivery and reliability are important. It is the backbone to many of the most popular protocols.
    
    UDP is useful when transmission speed is important and the integrity of the data isn’t as important, or is managed by a higher layer. An example is UDP-based applications; typically the application is responsible for data integrity.
    
    SCTP is an evolving protocol designed for efficient robust communication. Some of the features are still being sorted such as using SCTP through a NAT firewall, you can see the [status of SCTP-NAT](https://datatracker.ietf.org/doc/draft-ietf-tsvwg-natsupp/) on a Datatracker website.
    
    For a more detailed look at SCTP reference RFC4960 sctp.
    
    | CHARACTERISTICS | TCP | UDP | SCTP |
    | --- | --- | --- | --- |
    | Connection-oriented | Yes | No | Yes |
    | Reliable | Yes | No | Yes |
    | Ordered delivery | Yes | No | Yes |
    | Checksums | Yes | Optional | Yes |
    | Flow control | Yes | No | Yes |
    | Congestion avoidance | Yes | No | Yes |
    | NAT friendly | Yes | Yes | Not yet |
    | ECC | Yes | Yes | No |
    | Header size | 20-60 bytes | 8 bytes | 12 bytes |
    
    # **OSI Layer 5: Session Layer**
    
    **Layer 5** or **Session Layer** is used for establishing, managing, synchronizing and termination of application connections between local and remote application. If an established connection is lost or disrupted, this layer may try to recover the connection. If a connection is not used for a long time, the session layer may close and reopen it. There are two types of sessions: connection-mode service and connectionless-mode sessions.
    
    Session options:
    
    - Simplex or duplex communication
    - Transport Layer reliability
    - Checkpoints of data units
    
    Session services may be involved in:
    
    - Authentication for Transport Layer
    - Setup and encryption initialization
    - Support for steaming media
    - Support for smtp,http and https protocols
    - SOCKS proxy
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/qndnihl4tdgg-OSI-Layer-5a.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/qndnihl4tdgg-OSI-Layer-5a.png)
    
    **Session Layer**
    
    The Session Layer creates a semi-permanent connection which is then used for communications, many of the RPC-type protocols depend on this layer:
    
    - NetBIOS: Network Basic Input Output System
    - RPC: Remote Procedure Call
    - PPTP: Point to Point Tunneling Protocol
    
    # **OSI Layer 6: Presentation Layer**
    
    The **Presentation Layer** is commonly rolled up into a different layer, Layer 5 and/or Layer 7.
    
    For example, the HTTP protocol (an Application Layer protocol) has methods for converting character encoding. In other words this Presentation Layer step happens at the Application Layer.
    
    Some of the protocols that function at this level include:
    
    - AFP, Apple filing
    - NCP, Netware Core
    - x25 PAD, x25 Packet Assembler/Disassembler
    
    Some of the services that may be available at the Presentation level are:
    
    - Data Conversion (EBCDIC to ASCII)
    - Compression
    - Encryption/Decryption
    - Serialization
    
    # **OSI Layer 7: Application Layer**
    
    The **Application Layer** is the most well-known. This layer is the top of the stack and deals with the protocols which make a global communications network function.
    
    Common protocols which exist in the Application Layer are:
    
    - HTTP: Hypertext Transfer Protocol
    - SMTP: Simple Mail Transfer Protocol
    - DNS: Domain Name System
    - FTP: File Transfer Protocol
    - DHCP: Dynamic Host Configuration Protocol
    
    # ****System Services****
    
    Many system services are provided by daemons which run at the Application Layer (Layer 7). Different initialization systems have been built to ensure that system daemons are running.
    
    **Initialization Systems**
    
    BSD init scripts
    
    Originally BSD init scripts were very few and large utilizing only 2 or 3 scripts to complete system initialization and start the system services. FreeBSD uses traditional BSD [startup](https://docs.freebsd.org/en/articles/linux-users/#startup).
    
    SysVinit
    
    [SysVinit](https://wiki.archlinux.org/title/SysVinit), possibly the most common initialization mechanism for many years and still in use today. It implements the concept of *runlevels*, and small control scripts for each service that are launched depending on the runlevel.
    
    Upstart
    
    [Upstart](http://upstart.ubuntu.com/) is a re-implementation of the SysVinit, maintaining file compatibility and introducing extra features like dependency checking if the control scripts were updated. Upstart was popular for only a short period of time.
    
    OpenRC
    
    [OpenRC](https://wiki.artixlinux.org/Main/OpenRC) uses SYSV type scripts to manage the system services. The size of OpenRC and simplistic configuration make is desirable in some system environments.
    
    systemd
    
    [systemd](https://www.freedesktop.org/wiki/Software/systemd/) is a new implementation. It uses small config files that detail programs and options to run, the required dependencies, sequence of initialization and other enhancements. systemd has grown to encompass many extra services as well as the traditional service control.
    
    Most Enterprise Linux distributions use systemd for initialization and service management, and it is also used in this course.
    
    # **System V Init Scripts**
    
    The SYSV initialization scripts, or a close copy are used by most of the initialization environments such as:
    
    - Upstart can use existing SYSV scripts
    - OpenRC claims the scripts are similar to SYSV but easier to configure
    - systemd has a service dedicated to using existing SYSV scripts
    
    So why not just use SYSV? Here are some of the issues addressed by newer implementations:
    
    - Shell scripts used to start services
    - Serial execution
    - Dependency management done manually
    - The execution order and dependencies for SYSV init scripts are managed by renaming or making soft links to scripts in a single directory to run in proper order
    - Long boot times due to serial execution
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/rb83anpjfxss-sysV_init.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/rb83anpjfxss-sysV_init.png)
    
    **SYSV Start Sequence**
    
    The associated graphic highlights an example of the configuration used by SysVinit. When the init process begins, it consults a file **/etc/inittab** for a run level. Based on the run level, init opens a directory of crafted script names that have function indicators built into the name. The crafted names are generally soft inks pointing to a single directory. SysVinit opens the associated directory **/etc/rc.3.d** and processes the scripts based on their sort order. The first character being a K or a S indicating Start or Kill of the listed service. The number between S or K is used to sequence the start and stop of the service.
    
    # **Managing System Services with systemd**
    
    The **systemctl** is the main command for controlling system services. When installed, systemd provides the init program used during system initialization and is responsible for setting up the system as defined in the various configuration files.
    
    The systemd standard management functions for a systems service are the following commands:
    
    - Start the service **`# systemctl start httpd.service`**
    - Stop the service **`# systemctl stop httpd.service`**
    - Enable the service to auto start on system boot **`# systemctl enable httpd.service`**
    - Disable or prohibit a service from auto starting **`# systemctl disable httpd.service`**
    - Obtain the status of a service `**# systemctl status httpd.service**`
    - Run the service in foreground for debugging **`# /usr/sbin/httpd -f /etc/httpd/httpd.conf`**
    
    # **Additional systemd Utilities**
    
    In addition to starting and stopping services, there are some built-in and external function commands available for more information.
    
    - List the services (units) currently loaded `**# systemctl**`
    - List the sockets in use by systemd launched services **`# systemctl list-sockets`**
    - List the timers currently active **`# systemctl list-timers`**
    - Set specific unit runtime values if supported; this will set properties used for resource control if enabled **`# systemctl set-property foobar.service CPUWeight=200 MemoryMax=2G IPAccounting=yes`**
    - Display the status, state, configuration file(s) and last few log messages for this service **`# systemctl status httpd.service`**
    - Find overridden configuration files and display the differences `**# systemd-delta**`
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/2j5o2ojpk8zr-systemctl-status.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/2j5o2ojpk8zr-systemctl-status.png)
    
    **Systemctl Status**
    
    # **Transient System Services**
    
    Some services are not used enough to keep a daemon running full time. The **xinet** daemon was created to manage these transient daemons.
    
    Xinetd is started by using the system-specific init system.
    
    It has two or more configuration files to consider. The default configuration file is **`/etc/xinetd.conf`**. Additional per-service configuration files reside in **`/etc/xinetd.d/`** directory.
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/g2ivcgrty0qm-xinetd.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/g2ivcgrty0qm-xinetd.png)
    
    **Transient System Services**
    
    Xinet listens on the proper port and when a connection is made, it starts the managing daemon and passes off the connection.
    
    More information is available in **man 8 xinetd** pages.
    
    In most distributions xinet functionality has been absorbed by systemd.
    
    # **Systemd Service Customization**
    
    Systemd has a flexible configuration architecture that uses override and drop-in directories. The sequence that systemd processes the configuration files is predictable and extensible. The common sequence is:
    
    - The vendor or package supplied unit files are generally in one of the following directories: `**/usr/lib/systemd/system/<service>.service** or **/lib/systemd/system/<service>.service**`
    - Optional or dynamically created unit files in **`/run/systemd/system/`** directory
    - Optional user unit override files in **`/etc/systemd/system/`** directory
    - Optional user drop-in files in **`/etc/systemd/system/<service>.d`**
    
    There are drop-in directories available for all of the configuration directories but the most popular drop-in location is in **`/etc/systemd/system/<service>.d/**.`
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/ms6w8cfqbjc1-systemd-config-1.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/ms6w8cfqbjc1-systemd-config-1.png)
    
    **Systemd Service Configuration**
    
    ![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/asifb4scxgk5-Whenasystemdserviceisinstalledadditionalunitfilesordropindirectoriesmaynotbecreated1.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/asifb4scxgk5-Whenasystemdserviceisinstalledadditionalunitfilesordropindirectoriesmaynotbecreated1.png)
    
    A full description of the directories is available from **`man 5 systemd-system.config**.`