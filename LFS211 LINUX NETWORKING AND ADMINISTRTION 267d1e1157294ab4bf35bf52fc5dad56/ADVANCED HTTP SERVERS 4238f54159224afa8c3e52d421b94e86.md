# ADVANCED HTTP SERVERS

Created: April 18, 2023 12:24 PM
Status: Not started

# **Learning Objectives**

By the end of this section, you should be able to:

- Explore some of the more advanced configurations of Apache using:Rewrite moduleStatus moduleAlias moduleInclude module.

# **Rewriting URLs**

URL rewriting is a powerful way to modify the request that an Apache process receives. Modifications can include moved documents, forcing SSL, forcing login, human-readable URIs, and redirecting based on the browser.

Because of the power behind **mod_rewrite**, rewriting URLs can be complex. You might want to take a look at the [Apache Documentation](http://httpd.apache.org/docs/current/rewrite/) to learn more about this process.

Rewrite rules can exist in the **.htaccess** files, the main configuration file, or **<Directory>** stanzas. The **.htaccess** files seem the most common, but the main configuration file may be more secure.

The **mod_rewrite** uses PCRE (Perl Compatible Regular Expressions). This provides nearly unlimited conditions or filters to include, like:

- Time of day
- Environment variables
- URLs or query strings
- HTTP headers
- External scripts.

# **Rewrite Map**

Use a rewrite map for advanced or dynamic rewrite rules. The [Apache Documentation](http://httpd.apache.org/docs/current/rewrite/rewritemap.html) covers each of them in detail.

Pre-compiled rewrite maps exist for specific purposes. To learn more, see the [WURFL (Wireless Universal Resource FiLe)](http://wurfl.sourceforge.net/) map.

Rewrite maps are used when rewrite rules are too complex or numerous. There are many different types of maps:

- **txt**: Flat text
- **dbm**: DBM hash (indexed)
- **rnd**: Randomized plain text
- **prg**: External program.

# **Rewriting Examples**

Next, we will provide several rewriting examples. To learn more about the RewriteRule option flag, check the [documentation](https://httpd.apache.org/docs/2.4/rewrite/flags.html).

To turn on the rewrite engine, use the following command:

**`RewriteEngine on`**

To force a redirect to a new location for a document, use the following command:

**`RewriteRule ˆ/old.html$ new.html [R]`**

To rewrite a URI based on the web browser (without a redirect), use the following command:

**`RewriteCond %{HTTP_USER_AGENT} .*Chrome.*RewriteRule ˆ/foo.html$ /chrome.html [L]`**

To create a human-readable URI from a CGI script (the PT or Passthrough flag: without it, the rewrite is treated as a file, not a URI), use the following command:

**`RewriteRule ˆ/script/(.*) /cgi-bin/script.cgi?$1 [L,PT]`**

# **mod_alias**

The **`mod_alias**` command maps URIs to filesystem paths outside of normal **`DocumentRoot`**. It also provides **`Alias**` and **`Redirect**` directives, Apache access control considerations, file permission considerations, and SELinux considerations.

**`mod_alias**` is much simpler than **`mod_rewrite`**. A redirect directive sends a redirect that is similar to the **R** flag for **`mod_rewrite`**.

To serve the files located at **/newdocroot/** under the URI **/new/** (the **<Directory>** stanza for access control modifications), use:

```bash
Alias /new/ /newdocroot/
<Directory /newdocroot/>
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

Care must be taken to properly set the SELinux, Apache access controls, and filesystem permissions to allow Apache to serve content from the directory if it is not inside the default document root. Aliases are applied in order, so more-specific aliases should be listed first or they will be overridden by the less specific ones.

# **AliasMatch**

**`AliasMatch**` is like the **`Alias**` directive with regular expressions. It is more powerful than **`Alias`**, but not as powerful as a **`rewrite`**.

To serve the files located at `**/newdocroot/<SUBDIR>**` under the URI **`/new/<SUBDIR>`** with different permissions for each use:

```bash
AliasMatch \ˆ{}/new/(.*) /newdocroot/$1/
<Directory /newdocroot/foo/>
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
<Directory /newdocroot/bar/>
    Options -Indexes FollowSymLinks
    Require all granted
</Directory>
```

# **AliasMatch Considerations**

**`AliasMatch**` is not a direct replacement for **`Alias`**. Because **`AliasMatch**` uses a regular expression, `**Alias /new/ /newdocroot/**` is not the same as **`AliasMatch /new/ /newdocroot/**.`

The latter will send all URIs which contain the string **`/new/`** anywhere to **`/newdocroot/**.`

The proper way to replace **`Alias**` with **`AliasMatch**` is the following command:

**`AliasMatch ˆ/new/(.*)$ /newdocroot/$1`**

# **ScriptAlias**

Common Gateway Interface (CGI) scripts should not be in your document root.

There are two ways to run scripts outside your document root:

- Create an alias and set the handler to **`cgi-script**.`
- Use **`ScriptAlias`**.

Use the same considerations as **`Alias**` and **`AliasMatch**:`

- Apache access control considerations
- File permission considerations
- SELinux considerations.

Because of the nature of CGI scripts, they should not be directly served as plain files.

**`ScriptAlias**` will set up an alias and enable the **`cgi-script`** handler. An example of this is:

```bash
ScriptAlias /newscripts/ /newscripts-docroot/
<Directory /newscripts-docroot/>
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

# **mod_status**

**`mod_status**` has the following characteristics:

- Apache server status
- Human-readable page
- Machine-readable page
- Security considerations with access control and extended status.

To enable **`mod_status`**, use:

```bash
<Location /server-status>
  SetHandler server-status
  Require ip 10.100.0.0/24
</Location>
```

To get the machine-readable version of the status page, append **`auto**` to the URI you have defined above. Because of the nature of this information, it should be restricted to trusted hosts or networks.

Apache version 2.3.6 and later changed the default for the **`ExtendedStatus**` directive to be on

# **mod_include**

**`mod_include**` provides filtering before a response is sent to the client. It is enabled with the **`+Includes`** directive. By default, this is enabled only for **`*.shtml`** files, but it can be enabled for all **`*.html`** files with the **`XBitHack`** directive.

To enable **`includes**` use:

```bash
<Location /dynamic/>
   Options +Includes
   XBitHack on
</Location>
```

An HTML file which had includes would contain:

```bash
<!--#set var="dude" value="very yes" -->
...
<!--#echo var="dude" -->
...
<!--#include virtual="http://localhost/includes/foo.html" -->
```

# **mod_perl**

**`mod_perl**` is a Perl interpreter embedded in Apache. It can provide full access to the entire Apache API, sharing information between Apache processes. CGI scripts can run unmodified.

Some of the downsides to using **`mod_perl**` are:

- Some CPAN (Comprehensive Perl Archive Network) modules are not thread-safe, therefore they do not work with the worker MPM (Multi-Processor Module).
- Some Perl methods like **`die()`** and **`exit()`** can cause performance issues.

To enable **`mod_perl`** use:

```bash
Alias /perl /var/www/perl
<Directory /var/www/perl>
   SetHandler perl-script
   PerlResponseHandler ModPerl::Registry
   PerlOptions +ParseHeaders
   Options +ExecCGI
</Directory>
```

For a sample Perl script using `**mod_perl**` use:

```bash
print "Content-type: text/plain \n";
print "mod_perl 2.0 rocks! \n";
```

# **Multi-Processing Modules (MPMs)**

To allow for different performance characteristics, different multi-processing modules can be used.

The **`Prefork**` MPM is the default MPM on most Linux systems. Prefork is non-threaded, and is thus safe for libraries and scripts which are not thread-safe. Because each request is handled by a separate process, the Prefork MPM uses more memory and resources to handle the same number of connections as the Worker MPM.

The **`Worker**` MPM is a hybrid multi-process, multi-threaded model. Each sub-process spawns threads which handle the incoming connections. Because threads are lighter on system resources than processes, this module has better performance with less resource utilization than the Prefork MPM.

The **`Event**` MPM is based on the Worker MPM but it off-loads some of the request handling to shared processes, thus increasing performance yet again.

# ****Configuring Prefork****

**StartServers**
Number of child server processes to create on startup.

**MinSpareServers**
Minimum number of child server processes to keep on hand to serve incoming requests.

**MaxSpareServers**

****Maximum number of child server processes to keep on hand to serve incoming requests. Any spare processes above this number are killed.

**MaxRequestsPerChild**
Maximum number of requests a child server process serves.

**MaxClients**

Maximum number of simultaneous connections to serve. To increase this number, you may also have to increase the **`ServerLimit**` directive.

# ****Configuring Worker****

**StartServers**
Number of child server processes to create on startup.

**MaxClients**
Maximum number of simultaneous connections to serve.

**MinSpareThreads**
Minimum number of worker threads to keep on hand to serve incoming requests.

**MaxSpareThreads**
Maximum number of worker threads to keep on hand to serve incoming requests.

**ThreadsPerChild**
Constant number of worker threads in each child server process.

**MaxRequestsPerChild**
Maximum number of requests a server process serves.

# **Load Testing**

Apachebench (**`ab`**) is the tool provided as a part of the Apache httpd server package.

The **`httperf**` tool was written at [Hewlett Packard's Labs](https://www.labs.hpe.com/next-next). [Httperf](https://github.com/httperf/httperf) can use log files as a source of URIs to test.

For more information about **siege**, review the [Joe Dog Software](https://www.joedog.org/siege-home/) website.

A tool named **`sproxy**` works as an HTTP proxy server, collecting all the information and URIs to use as the Siege testing corpus.

The best testing results come from using URIs which match the real-life workflow. Some of these tools can use log files directly, others collect information in other ways. Having a proper testing corpus is almost as important as the settings you use.

# **Caching and Proxies**

Apache can act as a forward or reverse proxy that is managed by **`mod_proxy**` and **`mod_proxy_http`**.

Debugging headers include:

**`X-Forwarded-ForX-Forwarded-HostX-Forwarded-Server`**

To enable a reverse proxy to an internal server, use:

```bash
<Location /foo>
    ProxyPass ht‌tp://appserver1.example.com/foo
</Location>
```

You can also use the following alternative syntax:

```bash
ProxyPass /foo ht‌tp://appserver1.example.com/foo
```

f the first argument to `ProxyPass` has a trailing **`/`**, then the second should have it as well. There are special-purpose tools which do caching much more efficiently:

- Varnish: A RAM and disk-based cache which uses the Linux kernel's swapping mechanism to speed up caching.
- Squid: A general-purpose caching proxy.

# ****Speciality HTTP Servers****

Specialty HTTP servers have been developed to deal with different issues.

The C10k Problem describes the issues in making a web server which can scale to ten thousand concurrent connections/requests.

**cherokee**

[cherokee](http://cherokee-project.com/) has an innovative web-based configuration panel. Benchmarks have shown cherokee to be much faster than Apache for dynamic and static content.

lighttpd

[lighttpd](https://www.lighttpd.net/) is used to power some high-profile sites, such as YouTube and Wikipedia. lighttpd has a light-weight and scalable architecture as well.

# W**eb Server Balancer**

When the amount of computing work to prepare a response loads the HTTP server too much, the work can be split up across many servers using **balancing**:

- The world accessible address is to the machine configured as a **load balancer**.
- The load balancer selects which minion to dispatch with the request.
- The selected minion acquires the data to satisfy the request.
- The minion sends the answer to the requester.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/fcuv7w7tvba0-ScreenShot2020-04-27at11.47.12AM.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/fcuv7w7tvba0-ScreenShot2020-04-27at11.47.12AM.png)

**HTTP Load Balancer**

# **Web Server Balancer Configuration**

The Apache load balancer requires the modules:

- **`mod_ proxy`** and additional protocol modules:mod_proxy_http
- mod_proxy_ftp
- mod_proxy_ajp
- mod_proxy_wstunnel

**mod_proxy_balancer** and one of the load balancing schedulin

modules:mod_libmethod_byrequests (request counting)

- mod_libmethod_traffic (weighted traffic counting)
- mod_libmethod_bybusyness (pending request counting)
- mod_libmethod_heartbeat (heartbeat traffic counting)

A minimalistic configuration for the load balancer could be something like the configuration below:

```bash
<VirtualHost *:80>
        ProxyRequests off
        ServerName www.somedomain.com

        <Proxy balancer://mycluster>
                BalancerMember ht‌tp://10.176.42.144:80 #minion1
                BalancerMember ht‌tp://10.176.42.148:80 #minion2
                BalancerMember ht‌tp://10.176.42.150:80 #minion3

                Require all granted # all requests are allowed

                ProxySet lbmethod=byrequests # balancer setting, round-robin

        </Proxy>

        ProxyPass / balancer://mycluster/

</VirtualHost>
```