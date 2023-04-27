# ADVANCED NETWORKING

Created: April 26, 2023 9:22 PM
Status: Not started

# **Learning Objectives**

By the end of this section, you should be able to:

- Explain the concept of routing.
- Configure temporary and persistent routes.
- Configure VLAN.
- Discuss the DHCP configuration.
- Configure an NTP server.

# **Routing**

Routing commands include **`route`** and **`ip route`**. Dynamic routing protocols include RIP, OSPF, BGP and IS-IS.

To create a new run time route, do the following command:

**`# ip route add 10.1.11.0/24 via 10.30.0.101 dev eth2`**

To create a static rule which will survive a reboot, do the following:

- On CentOS, edit the **`/etc/sysconfig/network-scripts/route-<INTERFACE>`** file and add a line like this: `**10.1.11.0/24 via 10.30.0.101 dev eth2**`
- On Ubuntu, edit the **`/etc/network/interfaces`** file and add a line like this:

**`up route add -net 10.1.11.0/24 gw 10.30.0.101 dev eth2`**

- On OpenSUSE, edit the **`/etc/sysconfig/network/ifroute-<INTERFACE>`** file and add a line like this: `**10.1.11.0/24 10.30.0.101 - eth2**`
- Network Manager systems can use this command:

**`# nmcli con modify "connection-name" ipv4.routes "10.42.1.0/24 192.168.122.10 99"`**

The downside to static routes is inflexibility. Dynamic routing protocols are more efficient at detecting and fixing routing problems quickly. Configuring dynamic routing is more complex. Several tools exist to assist in configuring dynamic routing. For example, the Quagga and Zebra projects seem to be popular.

# **Virtual Local Area Network (VLAN)**

VLANs use functionality in the switches and routers. The switch or router functionality must exist in the device as it is not usually an option that be added later.

The most common use for VLANs is to bridge switches together. Creating a trunk connection between two switches essentially connects the networks together. VLANs can be created on the trunked together switches to then isolate specific ports on each switch to belong to a Virtual LAN.

VLANs use optional functionality within the packet to identify which VLANs are being used.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/06bq51dt8pu8-VLAN-eps-converted-to.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/06bq51dt8pu8-VLAN-eps-converted-to.png)

**VLAN**

# **VLAN: Packet Attributes**

A Virtual Local Area Network (VLAN) allows multiple network nodes to end up in the same broadcast domain, even if they are not plugged into the same switch or switches. A VLAN is also a method for securing two or more LANs from each other on the same set of switches.

VLANs can be linked from point to point by doing VLAN trunking (802.1q is one such protocol).

When VLANs are enabled, an additional header is added to the packet. This is the 802.1Q or dot1q header. The 802.1Q header contains:

- Tag Protocol ID (TPID), a 16bit identifier to distinguish between a VLAN tagged frame or it indicates the EtherType. The value of x8100 indicates an 802.1Q tagged frame.
- The next 16bits are the Tag Control Information (TCI), comprising of:Priority Code Point (PCP), a 3bit field that indicates the 802.1q priority class. See the [IEEE P802.1Q](https://en.wikipedia.org/wiki/IEEE_P802.1p) webpage for more details.Drop Eligible Indicator (DEI). This 1bit flag indicates the frame may be dropped when network congestion occurs. The field may be used alone or in conjunction with the PCP. This field used to be the Canonical Format Indicator (CFI), and was used for compatibility between Ethernet and Token Ring frames.VLAN Identifier ID (VID), a 12bit field indicating to which VLAN the frame belongs to.

Additional information can be found on the [IEEE 802.1Q webpage](https://en.wikipedia.org/wiki/IEEE_802.1Q).

Below you can find VLAN packet details.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/wcyzmjoqtair-LFS311-V6_vlan-packet.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/wcyzmjoqtair-LFS311-V6_vlan-packet.png)

**VLAN: Packet Attributes**

# **Dynamic Host Configuration Protocol (DHCP) Server**

The Dynamic Host Configuration Protocol (DHCP) is used to configure the network-layer addressing. The **`dhcpd`** daemon used to be configured using both a configuration file (**`/etc/dhcp/dhcpd.conf`**) and a daemon options file that was distribution-dependent. Recent versions of **`dhcp**` have moved the daemon options into **`systemd`**.

The daemon options are configured in a separate file:

On CentOS **`/etc/sysconfig/dhcpd`**

On Ubuntu **`/etc/default/isc-dhcp-server`**

On OpenSUSE `**/etc/sysconfig/dhcpd**`

The **`dhcp`** server will only serve out addresses on an interface that it finds a subnet block defined in the `**/etc/dhcp/dhcpd.conf**` file. It is no longer a requirement to explicitly tell **`dhcp**` which interfaces to use.

Additional or different daemon command line options may be passed to the daemon at start time by the systems' drop-in files. Please see the `**COMMAND LINE**` section in the **`dhcpd man`** page for additional details.

# **DHCP Configuration**

Global options are settings which should apply to all the hosts in a network. You can also define options on a per-network basis.

A sample configuration would be

```bash
subnet 10.5.5.0 netmask 255.255.255.224 {
  range 10.5.5.26 10.5.5.30;
  option domain-name-servers ns1.internal.example.org;
  option domain-name "internal.example.org";
  option routers 10.5.5.1;
  option broadcast-address 10.5.5.31;
  default-lease-time 600;
  max-lease-time 7200;
  }
```

# **Network Time Protocol**

---

Many protocols require consistent, if not accurate time to function properly.

The security of many encryption systems is highly dependent on proper time. Industries such as commodities or stock trading require highly accurate time, as a difference of only seconds can mean hundreds if not thousands of dollars lost or earned. NTP time sources are divided up into strata.

- A **`strata 0**` clock is a special purpose time device (atomic clock, GPS radio, etc).
- A **`strata 1`** server is any NTP server connected directly to a **`strata 0**` source (over serial or the like).
- A **`strata 2`** server is any NTP server which references a **`strata 1`** server using NTP.
- A **`strata 3`** server is any NTP server which references a **`strata** 2` server using NTP.

NTP may function as a client, a server, or a peer:

- Client: Acquires time from a server or a peer.
- Server: Provides time to a client.
- Peers: Synchronize time between other peers, regardless of the defined servers.

Below you can see the Network Time Protocol illustrated.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/07ioed6c8pbg-LFS230-ntp.jpg](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/07ioed6c8pbg-LFS230-ntp.jpg)

**Network Time Protocol**

# **NTP Applications**

Implementation of NTP services varies widely between distributions. The most common elements of the configuration are similar for most popular time synchronization applications.

**/etc/ntp.confntp**

[NTP](https://trainingportal.linuxfoundation.org/learn/course/linux-networking-and-administration-lfs211/advanced-networking/ADDWEBSITEADDRESS) is the default application.

**/etc/chrony/confchrony**

**chrony** is designed to work in a wide range of environments, including intermittent network connections and virtual machines.

**systemd-timesyncd**

An **ntp** client is only included in the **systemd** package (**/etc/systemd/timesyncd.conf**).

You should consult the related **man** pages for configuration details. Since the various NTP applications may conflict with each other, you may want to consider having only one NTP application active at a time.

****Configuring ntpd Client and ntpd Server****

Configuring the ntpd Client

To configure the **`ntpd`** client, choose an NTP source as a server or peer, start the NTP daemon, and verify that time is synchronizing.

A good NTP server is only as good as its time source. The [NTP Pool Project](https://www.pool.ntp.org/en/) was created to alleviate the load that was crippling the small number of NTP servers. The pool directive is aware of round-robin DNS servers and allows a different IP address on each request. If the connection allows peers, the **`pool`** command will establish peers on its own.

The classic **`server`** directive may be used if desired.

To configure your NTP server to use the NTP pool, edit the **/etc/ntp.conf** file and add or edit the following settings:

```bash
driftfile /var/lib/ntp/ntp.drift
pool 0.pool.ntp.org
pool 1.pool.ntp.org
pool 2.pool.ntp.org
pool 3.pool.ntp.org
```

• The **`ntpdc` `-c peers`** command can show the time difference between the local system and configured time servers. The **`timedatectl`** command is in many distributions and may be used to query and control the system time and date.

Configuring the ntpd Server

To configure the **`ntpd`** server, allow clients to request time (**`restrict`**), set up access for peers, declare the local machine to be a time reference, regulate who can query the time server with **`ntpq`** and **`ntpdc`** commands (see CVE-2013-5211), and start the NTP daemon.

Client access to an **`ntpd`** server can be restricted:

```bash
# Default policy prevents queries
restrict default nopeer nomodify notrap noquery
# Allow queries from a particular subnet
restrict 123.123.x.0 mask 255.255.255.0 nopeer nomodify notrap
# Allow queries from a particular host
restrict 131.243.1.42 nopeer nomodify notrap noquery
# Unrestrict localhost 
	restrict 127.0.0.1
```

in the **`ntp.conf`**
 as well as defining servers to receive time peers may be defined. Two or more peers will keep consistent time between themselves in the event of disconnection to the outside time source:

```bash
peer 128.100.49.12
peer 192.168.0.1
```

It may be desirable to declare the local machine as a time source:

```bash
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```