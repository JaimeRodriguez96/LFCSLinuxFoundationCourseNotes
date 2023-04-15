# DOMAIN NAME SERVICE

Created: April 13, 2023 9:47 PM
Status: Not started

# **Learning Objectives**

By the end of this section, you should be able to:

- Review the operation of a DNS server.
- Install and configure a caching DNS server.
- Configure an authoritative zone on a DNS server.

# **Before DNS**

Before DNS, there was ARPANET. The original solution to a name service was a flat text file called **HOSTS.TXT**. This file was hosted on a single machine, and, when you wanted to get a copy, you pulled it from this central server using File Transfer Protocol (FTP) or a similar protocol.

This did not scale well at all.

# **/etc/hosts**

A descendant of the **HOSTS.TXT** file is the **hosts** file. On Linux, this file resides at **`/etc/hosts`**. It has a very simple syntax:

```bash
<IP ADDRESS> <HOSTNAME> [HOSTNAME or alias] ...
```

An example of an **`/etc/hosts`** file is the following:

```bash
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.100.42.4 laser4 laser4.example.com
10.100.42.1 mail mail.example.com
```

This **hosts** file usually takes precedence over other resolution methods:

- Per-server IP address to host database
- Administrator-managed

# **Domain Name System**

The Domain Name System (DNS) is a distributed, hierarchical database for converting DNS names into IP addresses. The key-value store can be used for more than just IP address information. The DNS protocol runs in two different modes:

- Recursive with caching mode
- Authoritative mode

When a network node makes a DNS query, it most often makes that query against a recursive, caching server. That recursive, caching server will then make a recursive query through the DNS database, until it comes to an authoritative server. The authoritative server will then send the answer for the query.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/jq1cp4ny4pui-DNS-Tree.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/jq1cp4ny4pui-DNS-Tree.png)

**DNS Database**

The DNS database consists of a tree-like, key-value store. The database is broken into tree nodes called Domains. These domains are managed as part of a zone. Zones are the area of the namespace managed by authoritative server(s). DNS delegation is done on zone boundaries.

# **Recursive DNS Query**

A theoretical recursive DNS query for **host1.foo.example.com.** would take the following steps:

1. The client makes a recursive request to the caching nameserver it has configured.
2. The caching nameserver makes a query for **host1.foo.example.com.** to the root (".") zone nameserver.
3. The root (".") zone nameserver points to the nameserver for the **com.** zone.
4. The caching nameserver makes a query for **host1.foo.example.com.** to the nameserver for the **com.** zone.
5. The **com.** zone nameserver sends a response that points to the nameserver for the **example.com.** zone.
6. The caching nameserver makes a query for **host1.foo.example.com.** to the nameserver for the **example.com.** zone.
7. The **example.com.** nameserver sends a response that points to the nameserver for the **foo.example.com.** zone.
8. The caching nameserver makes a query for **host1.foo.example.com.** to the nameserver for the **foo.example.com.** zone.
9. The **foo.example.com.** nameserver responds authoritatively for the address **host1.foo.example.com.** to the caching nameserver.
10. The caching nameserver stores all of these queries and their responses in a cache and responds back to the client with the answer to the original query.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/muu8okika9sp-DNS-Query.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/muu8okika9sp-DNS-Query.png)

**Recursive DNS Query**

# **Query/Record Types**

Different database record types hold different information. Specifying a query type of **ALL** will fetch all record types. Many Internet protocols depend on properly set up record types.

A (Address Mapping Records)
Return 32bit IPv4 address.

AAAA (IP Version 6 Address Records)
Return 128big IPv6 address.

CNAME (Canonical Name Records)
Return an alias to another name.

MX (Mail Exchanger Records)
Return the message transfer agents (mail servers) for a domain.

NS (Nameserver Records)
Delegate an authoritative DNS zone nameserver.

PTR (Reverse-Lookup Pointer Records)
Pointer to a canonical name (IP address to name).

SOA (Start of Authority Records)
Start of Authority for a domain (domain and zone settings).

TXT (Text Records)
Arbitrary human-readable text, or machine-readable data for specific purposes.

```
TYPE            value and meaning

A               1 a host address

NS              2 an authoritative name server

MD              3 a mail destination (Obsolete - use MX)

MF              4 a mail forwarder (Obsolete - use MX)

CNAME           5 the canonical name for an alias

SOA             6 marks the start of a zone of authority

MB              7 a mailbox domain name (EXPERIMENTAL)

MG              8 a mail group member (EXPERIMENTAL)

MR              9 a mail rename domain name (EXPERIMENTAL)

NULL            10 a null RR (EXPERIMENTAL)

WKS             11 a well known service description

PTR             12 a domain name pointer

HINFO           13 host information

MINFO           14 mailbox or mail list information

MX              15 mail exchange

```

****Forward and Reverse DNS Queries****

Forward DNS Queries

Forward DNS queries use **A** or **AAAA** record types and are most often used to turn a DNS name into an IP address.

A Fully Qualified Domain Name (FQDN) is the full DNS address in the DNS database, and the most significant part is first. In the FQDN below, the most significant part is the hostname - **foo**:

**foo.example.com**

Reverse DNS Queries

A reverse DNS query is used to turn an IP address into a DNS name. It uses a **PTR** record type and an **arpa**. domain in a DNS database:

- **in-addr.arpa.** domain for **IPv4**
- **ip6.arpa.** domain for **IPv6**.

In an IP address, the most significant part is on the right. Because an FQDN is left-to-right most significant, and an IP address is right-to-left, we have to translate an IP address to put it into the DNS database. For example:

**192.168.13.32**

becomes

**32.13.168.192.in-addr.arpa.**

and

**2001:500:88:200::10**

becomes

**0.1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.2.0.8.8.0.0.0.0.5.0.1.0.0.2.ip6.arpa.**

# **DNS Server Daemons**

DNS server daemons respond to information requests from clients. These server daemons include:

- djbdns - was created by D.J. Berstein. It is advertised as a very secure DNS alternative to BIND. The creator of djbdns has posted a reward for the first person to [find a security hole in the DNS](https://cr.yp.to/djbdns/guarantee.html).
- Dnsmasq
- Microsoft DNS server
- BIND (Berkeley Internet Name Domain)Since BIND9 is the de-facto standard for the Internet, BIND9 will be used in this course.

# **DNS Client (The ”Resolver”)**

Historically the DNS client or **/etc/resolv.conf** file nameserver entries were created at configuration time and never changed. This static configuration worked well for servers that never changed locations or IP addresses. Network configuration is more dynamic and the nameserver entries need to be adjusted. The following are some of the utilities that can and will change the behavior of the file **`/etc/resolv.conf**:`

- **`/etc/resolv.conf`**This is the traditional static file used to configure the resolver.
- Network Interface Configuration FilesIn most distros, the nameserver information may be added to the interface configuration file, overwriting or modifying the **`/etc/resolv.conf`** when the interface is started.
- DHCP ClientThe DHCP Server often provides nameserver information as part of the information sent to the DHCP client, replacing the existing nameserver records.
- resolvconf ServiceMost popular in Ubuntu, this service uses additional files like **`/etc/resolvconf.conf`** and a background service resolvconf.service to ”optimize” the contents of **`/etc/resolv.conf**.`
- dnsmasqSets up in ”mini” caching DNS server and may alter the resolver configuration to look at dnsmasq instead of the items listed in **`/etc/resolv.conf**.`
- systemd.resolvedAs of systemd version 233, the systemd-resolved available. It provides a DNS stub listener on IP address 127.0.0.53 on the loopback adapter and takes input from several files including: `**/etc/systemd/resolved.conf**`, **`/etc/systemd/network/*.network`** and any DNS information made available by other services like ”dnsmasq”.

# **BIND Configuration File**

Settings for the BIND configuration file are stored in the **/etc/named.conf** file on CentOS and OpenSUSE and in the **`/etc/bind/named.conf**` file on Ubuntu. The syntax is in the **named.conf(5)** **man** page.

BIND uses three styles of comments:

- **C** style**/*** comment here **/ .**
- **C++** style**//** to end of line
- **UNIX** style**#** to end of line.
- 

# ****BIND Configuration Options****

listen-on
Port and IP address to listen for connections.

listen-on-v6
IPv6 port and IP address to listen for connections.

allow-query
Controls hosts which can make queries of the server.

recursion
Controls the server acting as a recursive resolver.

forwarders
When acting as a recursive resolver, controls which nameservers we should query first.

forward-first
Controls where the first recursive query happens (forwarders or 'root' domain).

To check the configuration file for proper syntax, use the **named-checkconf** command. When **named** is running in a chroot, use the syntax **named-checkconf -t <CHROOTDIR>**.

The **named-checkconf** command can also test load any defined primary zone files by using the **-z** switch.

# **BIND as a Caching Nameserver**

Notice the location and names are different, but the configuration has the same elements; they just may be moved into different files. The key configuration file is **named.conf**.

To set up a caching nameserver:

To install the package, run the following command:

- On RedHat, CentOS, or Fedora **`# yum install bind`**
- On OpenSUSE **`# zypper install bind`**
- On Debian, Ubuntu, or Linux Mint **`# apt-get install bind9`**

Change the configuration to **listen** on a public interface (**listen-on**, **listen-on-v6**, **allow-query**):

- On RedHat, CentOS, or Fedora **`/etc/named.conf`**
- On OpenSUSE **`/etc/named.conf`**
- On Debian, Ubuntu, or Linux Mint **`/etc/bind/named.conf`**

Start the BIND daemon, run the following command:

- On RedHat, CentOS, or Fedora **`# systemctl start named`**
- On OpenSUSE **`# systemctl start named`**
- On Debian, Ubuntu, or Linux Mint **`# systemctl start bind9`**

# **BIND Zone Configuration**

When it comes to the BIND zone configuration, each authoritative zone needs a definition in **named.conf**. Secondary zones also are defined in **named.conf**.

A zone definition for a primary zone in **named.conf** looks like this:

```bash
zone "example.com." IN {
   type primary;
   file "example.com.zone";
};
```

A zone definition for a secondary zone in **named.conf** looks like this:

```bash
zone "foo.example." IN {
   type secondary;
   primary { 192.168.122.11; 192.168.131.45; };
};
```

# **Zone Files**

Authoritative zones are defined and must contain an SOA (Start of Authority) record. Zone files should contain an NS (nameserver) record.

Some of the syntax considerations include:

- The record format is: **"{name} {ttl} {class} {type} {data}"**
- Always use trailing "." on all fully-qualified domain names
- **@** special character
- **GENERATE** syntax
- Comment marker - ; to end of line.

Below you can see an output example of a zone file:

```bash
$TTL 60
@ IN SOA  ns1.example.com. admin.example.com. (
  2012012301 ; serial
  3H         ; refresh
  1H         ; retry
  2H         ; expire
  1M)        ; neg ttl 
  IN NS ns1.example.com.;
  IN A 192.168.23.43
  IN AAAA 2001:500:88:200::10
;generate host1 thru host10
$GENERATE 1-10 host$  IN  A 127.0.0.$
```

We would verify the above zone file by using the following command:

**`# named-checkzone example.com.`** 

**`/var/named/chroot/var/named/example.com.zone`**

The **@** character expands to whatever the zone name is.

This example is using the CentOS default-named **chroot** option for file location.

# **SOA Records**

The SOA (Start of Authority) is required for every zone. Special control fields include:

- Admin email
- Primary name server
- Serial number
- Timers (secondary server **refresh** settings)**Refresh**: How often to check for new serial from primary.**Retry**: How often to retry if no response from primary.**Expire**: How long to keep returning authoritative answers when we cannot reach the primary server.**Negative TTL**: How long to cache an NX domain answer.

The serial number is one of the more important things in the SOA record. It should increment every time you make a change to your zone file.

One of the ways to ensure your serial number is updated properly is to use the following format, **YYYYMMDDXX** where **XX** is a two-digit revision number.

The negative TTL (part of the SOA record) is the time your caching nameserver will hold onto a "this record does not exist" answer. To set the default TTL use the **$TTL** line at the beginning of the line.

# **Split Horizon or DNS View**

Sometimes referred to as **split horizon** a **DNS view**:

- can provide different DNS answers to requests depending on selection criteria
- uses multiple *zone* files with different data for the same zone
- is often used to provide DNS services inside a corporation using private or non-routable addresses.

DNS view can assist solving challenges with multiple domains on a single DNS server, one example is:

- A private network served by a DHCP service.
- Use the DDNS/RNDC options in the DHCP server to notify the DNS server an address has been assigned on the private network.
- The hostname of the system is updated in the DNS via RNDC.
- The default route is assigned to a router/firewall with Network Address Translation set to the external address of the firewall.
- The private network addresses should never be visible outside.
- Systems on the private network use DNS names to communicate to each other.
- Systems outside to the private network only see the public addresses and names.

# **DNS Views**

DNS view is an option in current versions of **bind**. A DNS view will cause the DNS server to respond to requests different data depending on the match criteria, such as source IP address.

There are a couple rules:

- If the view directive is used, *ALL zones must be in a view*.
- Usually restrictions are added to the view, but are not mandatory. Yes, you can declare a view that no effect, it is just a label.
- The **named.conf** file is processed sequentially. The file should be ordered most restrictive to least restrictive.
- Each zone in a view will have a separate zone file.

In the following graphic:

- If the source IP address of the request is from the subnet **172.16.0.0/16** then the DNS answer will come from the file **internal-stuff.org.zone**.
- If the source of the request is from any other subnet, then the address will be supplied from the file **external-stuff.org.zone**.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/8bjxdbon1hwq-ScreenShot2020-04-27at11.22.04AM.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/8bjxdbon1hwq-ScreenShot2020-04-27at11.22.04AM.png)

**DNS View: Different Answer Depending on Source**

# **View Configuration Considerations**

When deploying a view there are some considerations for success.

- Wrap the zone stanza with a **view** command.
- Add to the view a name.
- Add to the view conditional tests as required:**match-clients****match-destinations****match-recursive-only**

Syntax of the view clause:

```bash
view "view_name" [class]
  [ match-clients address_match_list ; ]
  [ match-destinations address_match_list ; ]
  [ match-recursive-only yes | no ; ]
  // view statements
  // zone clauses
;
```

Sample configuration snippet from a running server:

```bash
// views in the named.conf file
view "internal" {
      match-clients {127.0.0.0/8; 172.16.42.0/16; };
    zone "example.com" {
        type primary;
        file "/etc/bind/internals/example.com.zone";
    };
};
view "external" {
    match-clients { any; };
    zone "example.com" {
        type primary;
        file "/etc/bind/externals/example.com.zone";
    };
};
```

# **DNS Tools**

Tools for DNS testing are in three categories: server configuration testing, server information testing, and server update.

The client tools used for DNS testing require an active DNS server to send the questions to. DNS client tools formulate a standard DNS request and send it off to the default or named DNS server. The most common server on the Internet is bind.

Servers

- **bind** (**B**erkeley **I**nternet **N**ame **D**omain)May be called bind9 depending on the distribution.

Clients

- **dig**: **d**omain **i**nformation **g**roperQueries DNS for for domain name or ip address mapping.Output format resembles the records used by the DNS server and is excellent for debugging DNS queries.
- **host**Simple interface for DNS queries.Excellent for use in scripts.
- **nslookup**: **n**ame **s**erver **lookup**,Rescued from deprecation.Queries DNS for domain name or IP address mapping.Less verbose than dig.Available on many operating systems.
- **nsupdate**: **n**ame **s**erver **u**pdateSend updates to a name server.Requires authentication and permission.Uses DNSSEC.