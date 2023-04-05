# NETWORK DEVICES AND CONFIGURATION

Created: April 3, 2023 10:48 PM
Reviewed: No

# **Chapter Overview**

Network devices such as Ethernet and wireless connections require careful configuration, especially when there are multiple devices of the same type. The question of consistent and persistent device naming can become tricky in such circumstances. Recently, the adoption of new schemes has made the naming more predictable. A number of important utilities are used to bring devices up and down, configure their properties, establish routes, etc., and system administrators must become adept at their use.

# **Learning Objectives**

By the end of this chapter, you should be able to:

- Identify network devices and understand how the operating system names them and binds them to specific duties
- Use the **ip** utility to display and control devices, routing, policy-based routing, and tunneling
- Use the older **ifconfig** to configure, control, and query network interface parameters from either the command line or from system configuration scripts
- Understand the Predictable Network Interface Device Names (PNIDN) scheme
- Know the main network configuration files in the **/etc** directory
- Use Network Manager (**nmtui** and **nmcli**) to configure network interfaces in a distribution-independent manner
- Know how to set default routes and static routes
- Understand virtual network interfaces
- Understand DNS and name resolution
- Troubleshoot networks and run valuable diagnostic utilities

# **Network Devices**

Unlike block and character devices, network devices are not associated with special device files, also known as device nodes. Rather than having associated entries in the **/dev** directory, they are known by their names.

These names usually consist of a type identifier followed by a number, as in:

- **eth0**, **eth1**, **eno1**, **eno2**, etc., for ethernet devices.
- **wlan0**, **wlan1**, **wlan2**, **wlp3s0**, **wlp3s2**, etc., for wireless devices.
- **br0**, **br1**, **br2**, etc., for bridge interfaces.
- **vmnet0**, **vmnet1**, **vmnet2**, etc., for virtual devices for communicating with virtual clients.

Historically, multiple virtual devices could be associated with single physical devices; these were named with colons and numbers; so, **eth0:0** would be the first alias on the **eth0** device. This was done to support multiple IP addresses on one network card. However, with the use of **ip** instead of **ifconfig**, this method is deprecated and we will not pursue it. This is not compatible with IPv6.

The historical device naming conventions encountered difficulties, particularly when multiple interfaces of the same type were present. For example, suppose you have two network cards; one would be named **eth0** and the other **eth1**. However, which physical device should be associated with each name?

The simplest method would be to have the first device found be **eth0**, the second **eth1**, etc. Unfortunately, probing for devices is not deterministic for modern systems, and devices may be located or plugged in an unpredictable order. Thus, you might wind up with the Internet interface swapped with the local interface. Even if hardware does not change, the order in which interfaces are located has been known to vary with kernel version and configuration.

Many system administrators have solved this problem in a simple manner, by hard-coding associations between hardware (MAC) addresses and device names in system configuration files and startup scripts. While this method has worked for years, it requires manual tuning and had other problems, such as when MAC addresses were not fixed; this can happen in both embedded and virtualized systems.

# **ip**

**ip** is the command line utility used to configure, control and query interface parameters and manipulate devices, routing, tunnels, etc. It is preferred to the venerable **ifconfig** we discuss next, as it is more versatile, as well as more efficient because it uses netlink sockets, rather than ioctl system calls.

**ip** can be used for a wide variety of tasks. It can be used to display and control devices, routing, policy-based routing, and tunneling.

The basic syntax is:

**ip [ OPTIONS ] OBJECT { COMMAND | help }ip [ -force ] -batch filename**

where the second form can read commands from a designated file.

**ip** is a multiplex utility; the **OBJECT** argument describes what kind of action is going to be performed. The possible **COMMANDS** depend on which **OBJECT** is selected.

You can see below some of the main values of **OBJECT**.

| OBJECT | FUNCTION |
| --- | --- |
| address | IPv4 or IPv6 protocol device address |
| link | Network Devices |
| maddress | Multicast Address |
| monitor | Watch for netlink messages |
| route | Routing table entry |
| rule | Rule in the routing policy database |
| tunnel | Tunnel over IP |

# **Using ip: Examples**

The **ip** utility can be used in many ways. A few example commands and their applications are provided below.

Show information for all network interfaces:

**`$ ip link show`**

Show information for the **eth0** network interface, including statistics:

**`$ ip -s link show eth0`**

Set the IP address for **eth0**:

**`$ sudo ip addr add 192.168.1.7 dev eth0`**

Bring interface **eth0** down:

**`$ sudo ip link set eth0 down`**

Set MTU to 1480 bytes for interface **eth0**:

**`$ sudo ip link set eth0 mtu 1480`**

Set route to network:

**`$ sudo ip route add 172.16.1.0/24 via 192.168.1.5`**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/to9vihmph3xi-ipubuntu.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/to9vihmph3xi-ipubuntu.png)

**Screenshot of the ip -s link show ens33 and ip addr show commands and their outputs**

# **ifconfig**

**ifconfig** is a system administration utility long found in UNIX-like operating systems used to configure, control, and query network interface parameters from either the command line or from system configuration scripts. It is also often called by network startup scripts. It has been superseded by **ip** and some Linux distributions no longer install it by default. See the following commands and their applications.

Display information about all interfaces:

**`$ ifconfig`**

Display information only about the **eth0** interface:

**`$ ifconfig eth0`**

Set the IP address to **192.168.1.50** on interface **eth0**:

**`$ sudo ifconfig eth0 192.168.1.50`**

Set the **netmask** to 24-bit:

**`$ sudo ifconfig eth0 netmask 255.255.255.0`**

Bring interface **eth0** up:

**`$ sudo ifconfig eth0 up`**

Bring interface **eth0** down:

**`$ sudo ifconfig eth0 down`**

Set the MTU (Maximum Transfer Unit) to 1480 bytes for interface **eth0**:

**`$ sudo ifconfig eth0 mtu 1480`**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/zs58kpcfxc58-ifconfigrhel7.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/zs58kpcfxc58-ifconfigrhel7.png)

**Screenshot of the ifconfig command and its output**

# **Predictable Network Interface Device Names**

The Predictable Network Interface Device Names (PNIDN) is connected to **udev** and integration with systemd. There are now 5 types of names that devices can be given:

1. Incorporating Firmware or BIOS provided index numbers for onboard devicesExample: **eno1**
2. Incorporating Firmware or BIOS provided PCI Express hotplug slot index numbersExample: **ens1**
3. Incorporating physical and/or geographical location of the hardware connectionExample: **enp2s0**
4. Incorporating the MAC addressExample: **enx7837d1ea46da**
5. Using the old classic methodExample: **eth0**

For example, on a machine with two onboard PCI network interfaces that would have been **eth0** and **eth1** (commands and outputs below):

**`$ ip link show | grep enp`**

```bash
**2: enp4s2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT qlen 10003: 
enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000**
```

**`$ ifconfig | grep enp`**

```bash
**enp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500enp4s2: 
flags=4099<UP,BROADCAST,MULTICAST mtu> 1500**
```

These names are correlated with the physical locations of the hardware on the PCI system (command and output below):

**`$ lspci | grep Ethernet`**

```bash
**02:00.0 Ethernet controller: Marvell Technology Group Ltd. 88E8056 PCI-E Gigabit Ethernet Controller (rev 12)
04:02.0 Ethernet controller: Marvell Technology Group Ltd. 88E8001 Gigabit Ethernet Controller (rev 14)**
```

The triplet of numbers at the beginning of each line from the **lspci** output is the bus, device (or slot), and function of the device; hence it reveals the physical location.

Likewise, for a wireless device that previously would have been simply named **wlan0** (commands and outputs below):

**`$ ip link show | grep wl`**

```bash
**3: wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT qlen 1000**
```

**`$ lspci | grep Centrino`**

```bash
**03:00.0 Network controller: Intel Corporation Centrino Advanced-N 6205 [Taylor Peak] (rev 34)**
```

We see the same pattern. It is easy to turn off the new scheme and go back to the classic names. We will leave that as a research project. In what follows, we will mostly follow the classic names for definiteness and simplicity.

# **Network Configuration Files**

Each distribution has its own set of files and/or directories, and they may be slightly different, depending on your distribution version.

Red Hat

**`/etc/sysconfig/network`**

**`/etc/sysconfig/network-scripts/ifcfg-ethX`**

**`/etc/sysconfig/network-scripts/ifcfg-ethX:Y`**

**`/etc/sysconfig/network-scripts/route-ethX`**

Debian

**`/etc/network/interfaces`**

SUSE

**`/etc/sysconfig/network`**

When using systemd (systemd is getting more standardized), it is preferable to use Network Manager.

On newer Linux distributions, these configuration files are either non-existent, empty, or much smaller.

# **Network Manager Configuration Files**

Network Manager can use the traditional network files from several distributions. The networking profiles are supported by plugins. The key-value is the preferred file format.

There are several optional plugins for traditional configuration compatibility:

- **ifupdown** for **`/etc/network/interfaces`**
- **ifcfg-rh** for **/`etc/sysconfig/network-scripts`**
- **ifcfg-suse** is for simple compatibility for SUSE and openSUSE
- **key-file** is a generic replacement for system specific configuration files

There is a configuration option in **`/etc/NetworkManager/NetworkManager.conf`** in the **[main]** section that lists which plugins for configuration processing are to be used in a comma-separated list. For more details, see the [NetworkManager.conf documentation](https://developer-old.gnome.org/NetworkManager/stable/NetworkManager.conf.html).

# **Network Manager**

Once upon a time, network connections were almost all wired (Ethernet) and did not change unless there was a significant change to the system.

As a system was booted, it consulted the network configuration files in the **/etc** directory subtree in order to establish the interface properties such as static or dynamic (DCHP) address configuration, whether the device should be started at boot, etc.

If there were multiple network devices, policies had to be established as to what order they would be brought up, which networks they would connect to, what they would be called, etc.

As wireless connections became more common (as well as hotplug network devices such as on USB adapters), configuration became much more complicated, both because of the transient nature of the hardware and that of the specific networks being connected to.

However, modern systems often have dynamic configurations:

- Networks may change as a device is moved from place to place
- Wireless devices may have a large choice of networks to hook into
- Devices may change as hardware, such as wireless devices, are plugged in or turned on and off

The previously discussed configuration files were created to deal with more static situations and are very distribution-dependent. A step away from distribution-dependent interfaces and configuration files was a big advance.

While Network Manager still uses configuration files, it is usually best to rely on its various utilities for manipulating and updating them. Network Manager should be almost the same on different systems.

# **Network Manager Interfaces**

If you are using your laptop in a hotel room or a coffee shop, you are probably going to use whatever graphical interface your Linux distribution's desktop offers. Every Linux desktop (GNOME, KDE, XFCE, etc.) has one, usually reached by clicking on something in the horizontal panel. You can use this to select between different networks, configure security and passwords, turn devices off and on, etc.

If you are making a configuration change on your system that is likely to last for a while, you are likely to use **nmtui** (network manager text user interface), as it has almost no learning curve and will edit the underlying configuration files for you. It uses the **ncurses** library interface.

If you need to run scripts that change the network configuration, you will want to use **nmcli** (network manager command line interface). Or, if you are a command line junkie, you may want to use this instead of **nmtui**.

If the GUI is properly done, you should be able to accomplish any task using any of these three methods. However, we will focus on **nmtui** and **nmcli** because they are distribution-independent and hide any differences in underlying configuration files.

# **nmtui**

**nmtui** is rather straightforward to use. You can navigate with either the arrow keys or the tab key.

Besides activating or editing connections, you also set the system hostname. However, some operations, such as this, cannot be done by normal users and you will be prompted for the root password to go forward.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/eg1ct1oevp73-nmtui-main.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/eg1ct1oevp73-nmtui-main.png)

**nmtui Main Screen**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/t5vzltr4u2nd-nmtui-edit.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/t5vzltr4u2nd-nmtui-edit.png)

**nmtui Edit Screen**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/532ezg082z3n-nmtui-config.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/532ezg082z3n-nmtui-config.png)

**nmtui Wireless Configuration**

# **nmcli**

**nmcli** is the command line interface to Network Manager. You can issue direct commands, but it also has an interactive mode.

For many details and examples, you can visit the [Networking/CLI Fedora wiki webpage](https://fedoraproject.org/wiki/Networking/CLI) or you can type the following command:

**`$ man nmcli-examples`**

We will explore the use of **nmcli** in lab exercises.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/nx0rg77er4hg-nmcli.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/nx0rg77er4hg-nmcli.png)

**Screenshot of the nmcli --help command and its output**

# **Routing**

Routing is the process of selecting paths in a network along which to send network traffic. The routing table is a list of routes to other networks managed by the system. It defines paths to all networks and hosts; local packets are sent directly, while remote traffic is sent to routers.

To see the current routing table, you can use either **route** or **ip** command:

**$ route -n**

**$ ip route**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/21lrnejspc9b-routeubuntu.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/21lrnejspc9b-routeubuntu.png)

**Screenshot of the route -n and ip route commands and their outputs**

# **Default Route**

The default route is the way packets are sent when there is no other match in the routing table for reaching the specified network.

It can be obtained dynamically using DHCP. However, it can also be manually configured (static). With the **nmcli** command it can be done via:

**`$ sudo nmcli con mod virbr0 ipv4.routes 192.168.10.0/24 +ipv4.gateway 192.168.122.0$ sudo nmcli con up virbr0`**

or you can modify configuration files directly, thus avoiding to modify files in **/`etc**` by hand.

On Red Hat-derived systems, you can modify the **`/etc/sysconfig/network`** file by putting in the line:

**`GATEWAY=x.x.x.x`**

or alternatively in **`/etc/sysconfig/network-scripts/ifcfg-ethX`** on a device-specific basis in the configuration file for the individual NIC.

On Debian-derived systems, the equivalent is putting:

**`gateway=x.x.x.x`**

in the `**/etc/network/interfaces**`file.

On either system, you can set the default gateway at runtime with these commands:

**`$ sudo route add default gw 192.168.1.10 enp2s0$ route`**

Output:

```bash
Kernel IP routing table
Destination     Gateway       Genmask       Flags Metric Ref Use Iface
default         192.168.1.10  0.0.0.0       UG    0      0     0 enp2s0
default         192.168.1.1   0.0.0.0       UG    1024   0     0 enp2s0
172.16.132.     0 0.0.0.0     255.255.255.0 U     0      0     0 vmnet1
192.168.1.0     0.0.0.0       255.255.255.0 U     0      0     0 enp2s0
192.168.113.0   0.0.0.0       255.255.255.0 U     0      0     0 vmnet8
```

Note that this might **wipe out** your network connection! You can restore either by resetting the network, or in the above example by running this command:

**`$ sudo route add default gw 192.168.1.1 enp2s0`**

These changes are not persistent and will not survive a system restart.

# **Static Routes**

Static routes are used to control packet flow when there is more than one router or route. They are defined for each interface and can be either persistent or non-persistent.

When the system can access more than one router, or perhaps there are multiple interfaces, it is useful to selectively control which packets go to which router.

Either the **route** or **ip** command can be used to set a non-persistent route as in:

**`$ sudo ip route add 10.5.0.0/16 via 192.168.1.100`**

On a Red Hat-based system, a persistent route can be set by editing the **/`etc/sysconfig/network-scripts/route-ethX`** file as shown by the following command and its output:

**`$ cat /etc/sysconfig/network-scripts/route-eth010.5.0.0/16 via 172.17.9.1`**

On a Debian-based system you need to add lines to the `**/etc/network/interfaces**` file, such as:

```bash
iface eth1 inet dhcp
post-up route add -host 10.1.2.51 eth1
post-up route add -host 10.1.2.52 eth1
```

On a SUSE-based system you need to add to or create a file such as **`/etc/sysconfig/network/ifroute-eth0**` with lines like:

```bash
# Destination Gateway Netmask Interface [Type] [Options]
192.168.1.150 192.168.1.1 255.255.255.255 eth0
10.1.1.150 192.168.233.1.1 eth0
10.1.1.0/24 192.168.1.1 - eth0
```

where each field is separated by tabs.

# **Teaming and Bonding Network Interfaces**

Teaming and Bonding interfaces allow dynamic network interface configuration to provide higher throughput and additional robustness to the network.

- BondingUses a kernel module to provide aggregation of network links to provide fail-over, redundancy and performance enhancements
- TeamingIs similar to bonding, except teaming uses a userspace service named teamed. The Teaming configuration method has been listed as deprecated and is scheduled to be removed in the future.

To facilitate a smooth conversion from Teaming to Bonding, the **team2bond** utility is available to convert configuration files.

# **Bonding Network Interfaces**

There are several methods for configuring Bonding interfaces:

- **sysfs**Direct changes to the **/sys** pseudofilesystem. Changes are not saved.
- **iproute2**Supports bonding via the **ip** command; see **man 8 ip-link** for details. Changes are not saved.
- **NetworkManager**All NetworkManager user interfaces support bonding. Changes are saved. The NetworkManager configuration files can also be edited with your favorite text editor.

# **Creating Bonding Network Interface with nmcli**

Creating a configuration for a bonding interface with **nmcli** involves a few steps. Minimal steps are:

1. Identify adapters: Use **nmcli device status**
2. Create bonding device: Use **nmcli connection add**
3. Attach interface to the bond: Create a connection from the adapter to the bond device using **nmcli connection add**
4. Set the bond adapter online: Issue an **nmcli connection up** command for the bond adapter
5. Reboot: Although the reboot is not absolutely required, it is strongly recommended. A reboot will clean up any configuration fragments left around

For more information on **nmcli**, check the [nmcli documentation](https://people.freedesktop.org/~lkundrak/nm-docs/nmcli.html).

You can see a bonding example using **nmcli** below. On the command line:

```bash
nmcli connection add type bond \
     con-name bond0 \
     ifname bond0 \
     bond.options "mode=active-backup,miimon=1000" 
nmcli connection add type ethernet slave-type bond \
     con-name bond0-port1 \
     ifname enp0s20u1u1 \
     master bond0
nmcli connection add type ethernet slave-type bond \
     con-name bond0-port2 \
     ifname enp0s20u1u2 \
     master bond0
nmcli connection up bond0
nmcli device status
reboot
nmcli device status
```

For more information on bonding and bonding options see the [Linux Ethernet Bonding Driver HOWTO documentation](https://www.kernel.org/doc/Documentation/networking/bonding.txt).

# **DNS and Name Resolution**

Name resolution is the act of translating hostnames to the IP addresses of their hosts. For example, a browser or email client will take training.linuxfoundation.org and resolve the name to the IP address of the server (or servers) that serve training.linuxfoundation.org in order to transmit to and from that location.

This can be done in either a static fashion using the **`/etc/hosts`** file, or dynamically using DNS servers.

There are several command line tools that can be used to resolve the IP address of a hostname:

**`$ [dig | host | nslookup] linuxfoundation.org`**

- **dig**: generates the most information and has many options
- **host**: more compact
- **nslookup**: older

**dig** is the newest and the others are sometimes considered deprecated, but the output for host is the easiest to read and contains the basic information.

One sometimes also requires reverse resolution: converting an IP address to a host name. Try feeding these three utilities a known IP address instead of a hostname, and examine the output.

# **/etc/hosts**

The **/etc/hosts** file holds a local database of hostnames and IP addresses. It contains a set of records (each taking one line) which map IP addresses with corresponding hostnames and aliases.

A typical file looks like:

```bash
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.1.100 hans hans7 hans64
192.168.1.150 bethe bethe7 bethe64
192.168.1.2 hp-printer
192.168.1.10 test32 test64 oldpc
```

Such static name resolution is primarily used for local, small, isolated networks. It is generally checked before DNS is attempted to resolve an address; however, this priority can be controlled by the **`/etc/nsswitch.conf`** file, but it is not often used today.

Command

**`student@ubuntu:/etc$ ls -l host*`**

Output:

```bash
-rw-r--r-- 1 root root  92 Oct 22 2015 host.conf
-rw-r--r-- 1 root root   7 Apr 21 08:46 hostname
-rw-r--r-- 1 root root 221 Apr 21 08:46 hosts
-rw-r--r-- 1 root root 411 Apr 20 17:14 hosts.allow
-rw-r--r-- 1 root root 711 Apr 20 17:14 hosts.deny
```

The other host-related files in **`/etc`** are **`/etc/hosts.deny`** and **`/etc/hosts.allow`**. These are self-documenting and their purpose is obvious from their names. The **allow** file is searched first and the **deny** file is only searched if the query is not found there.

**`/etc/host.conf**` contains general configuration information; it is rarely used.

# **DNS**

If name resolution cannot be done locally using the **`/etc/hosts`** file, then the system will query a DNS (Domain Name Server) server.

DNS is dynamic and consists of a network of servers which a client uses to look up names. The service is distributed; any one DNS server has only information about its zone of authority; however, all of them together can cooperate to resolve any name.

The machine's usage of DNS is configured in the **`/etc/resolv.conf`** file, which historically has looked like:

```bash
search example.com aps.org
nameserver 192.168.1.1
nameserver 8.8.8.8
```

which:

- Can specify particular domains to search.
- Defines a strict order of nameservers to query.
- May be manually configured or updated from a service such as DHCP (Dynamic Host Configuration Protocol).

Most modern systems will have an **`/etc/resolv.conf`** file generated automatically, such as:

```bash
# Generated by NetworkManager
192.168.1.1
```

which was generated by NetworkManager invoking DHCP on the primary network interface.

![Untitled](NETWORK%20DEVICES%20AND%20CONFIGURATION%20bf736cb6fa55411aa7c5b785d11c91c4/Untitled.png)

# **Networking Problem Troubleshooting**

Network problems can be caused either by software or hardware, and can be as simple as is the device driver loaded, or is the network cable connected. If the network is up and running but performance is terrible, it really falls under the banner of performance tuning, not troubleshooting. The problems may be external to the machine, or require adjustment of the various networking parameters, including buffer sizes, etc.

The following items need to be checked when there are issues with networking:

- **IP configuration**Use **`ifconfig**` or **`ip**` to see if the interface is up, and if so, if it is configured
- **Network Driver**If the interface cannot be brought up, maybe the correct device driver for the network card(s) is not loaded. Check with **lsmod** if the network driver is loaded as a kernel module, or by examining relevant pseudofiles in **`/proc`** and **`/sys`**, such as **`/proc/interrupts`** or **`/sys/class/net**.`
- ConnectivityUse **`ping**` to see if the network is visible, checking for response time and packet loss. **`traceroute**` can follow packets through the network, while **`mtr**` can do this in a continuous fashion. Use of these utilities can tell you if the problem is local or on the Internet.
- Default gateway and routing configurationRun **`route -n**` and see if the routing table make sense
- Hostname resolutionRun **`dig**` or **`host**` on a URL and see if DNS is working properly.

# **Network Diagnostics**

A number of basic network utilities are in every system administrator's toolbox:

- **ping** Sends 64-byte test packets to designated network hosts and tries to report back on the time required to reach it (in milliseconds), any lost packets, and some other parameters. Note that the exact output will vary according to the host being targeted, but at least you can see that the network is working and the host is reachable.
- **traceroute** Is used to display a network path to a destination. It shows the routers packets flow through to get to a host, as well as the time it takes for each hop.
- **mtr** Combines the functionality of **ping** and **traceroute** and creates a continuously updated display, like **top**.
- **dig** Is useful for testing DNS functionality. Note one can also use **host** or **nslookup**, older programs that also try to return DNS information about a host.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/u36pg612jn3z-Course_WarningBanners_Blank_regular7.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/u36pg612jn3z-Course_WarningBanners_Blank_regular7.png)

Example commands:

**`$ ping -c 10 linuxfoundation.org`**

**`$ traceroute linuxfoundation.org`**

**`$ mtr linuxfoundation.org`**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/mshyjuswwvl9-ping.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/mshyjuswwvl9-ping.png)

**Screenshot of the ping -c 10 linuxfoundation.org command and output**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/exn8zy7y7ll4-traceroute.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/exn8zy7y7ll4-traceroute.png)

**Screenshot of the traceroute google.com command and output**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/07wvx9dpddyk-mtr.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/07wvx9dpddyk-mtr.png)

**mtr Example**