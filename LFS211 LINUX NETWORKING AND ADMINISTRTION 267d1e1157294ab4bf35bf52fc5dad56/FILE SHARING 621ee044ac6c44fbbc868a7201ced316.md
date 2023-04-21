# FILE SHARING

Created: April 21, 2023 12:04 AM
Status: Not started

# ****File Transfer Protocol (FTP)****

**What Is FTP?**

The File Transfer Protocol (FTP) is one of the first protocols of the Internet. Data is sent in plain text. Clients have interactive or non-interactive modes. FTP has two data transfer modes:

- ActiveThe server pushes data to the client on a random high-numbered port. This method is not compatible with firewalls or NAT networks.
- PassiveThe client requests a passive connection and the server opens a random high-numbered port for data transfer.

**FTP Server**

Very Secure FTP Daemon (**vsftpd**) was suggested by SANS, IBM, and others for security reasons. Security concerns do not allow uploads and downloads of same content.

**vsftpd** features include:

- Virtual IPs
- Virtual users
- Stand-alone daemon or inetd-ready
- Per-user configuration
- Per-source configuration
- Optional SSL integration.

**Anonymous FTP**

To create an anonymous **`ftp**` download site, follow the steps below:

Verify that an **`ftp**` user exists, or create one. Creating an **`ftp**` user is sometimes done by distribution with running the following command: 

**`# getent passwd | grep ftp`**

Verify or create a default directory. Set permissions with the following command: 

**`# mkdir -p /var/ftp/pub# chown -R root:root /var/ftp/`**

**`# chmod 755 /var/ftp`**

**`# chmod 755 /var/ftp/pub`**

Verify that anonymous access is enabled in **`vsftpd.conf**:` 

**`anonymous_enable=YES`**

Start or restart the **`vsftpd**` service with the following command: 

**`# systemctl restart vsftpd`**

This is used for testing and debugging. Log in with **`anonymous**` user:

Logging: **`/var/log/vsftpd.log`**

**Authenticated FTP Sessions**

To allow system users to create authenticated FTP sessions, you should make the following changes to **`vsftpd.conf**:`

**`local_enable=YES`**

**`write_enable=YES`**

**`local_umask=022`**

**`vsftpd**` uses PAM by default for authentication. You may not need to make changes to the PAM configuration file, but if you do, edit the **`/etc/pam.d/vsftpd`** file.

System security is enforced by the files **`/etc/vsftpd/ftpusers`** (**`/etc/ftpusers`** on OpenSUSE) and **`/etc/vsftpd/users_list`**:

Account names listed in **`ftpusers**` are **`not**` allowed to login via FTP.

Depending on the value of the **`userlist_deny**` setting in **`vsftpd.conf`**, the users in **`/etc/vsftpd/users_list`** act as either allow only the users in the list, or explicitly deny the listed users.

# ****rsync****

What Is rsync?

The **rsync** protocol was written as a replacement for **rcp** and uses an advanced algorithm to intelligently transfer files. Only the files or parts of files which have changed are copied. **rsync** uses Delta encoding and requires the source and destination to be specified (either or both may be remote).

By itself, the **rsync** protocol does not have in-transit security. However, **rsync** can be tunneled over the SSH protocol.

An example of using **rsync** to list the available **rsync** mirror directories is provided below with the following command:

```bash
$ rsync rsync://mirror.csclub.uwaterloo.ca/
```

Refine the search to see the available ISOs with the following command:

```bash
rsync rsync://mirror.csclub.uwaterloo.ca/centos/7.6.1810/isos/x86_64/
```

Or perhaps download one of the ISOs with the following command:

```bash
rsync rsync://mirror.csclub.uwaterloo.ca/centos/7.6.1810/isos/x86_64/CentOS-7-x86_64-DVD-1810.iso /srv/centos/76/
```

rsync Daemon

Even though most use **`rsync**` over SSH, the **`rsync**` protocol by itself is very useful. Starting the **`rsync**` daemon is as easy as running the following command:

**`# rsync --daemon`**

When running as a daemon, **`rsync**` will read the **`/etc/rsyncd.conf`** configuration file. Each client that connects to the **`rsync**` daemon will force **`rsync**` to re-read the configuration. There is no need to restart the daemon when making configuration changes. Most distributions ship **`systemd**` files to control **`rsyncd**` as a system service. Usually it is included in the **`rsync**` package, but may also be split out into **`rsync**` daemon package in some distributions.

rsync Daemon Configuration

**`rsync**` daemon configurations include global settings (port, address and socket options) and module settings (path, comment and max connections).

The **`/etc/rsyncd.conf`** file defines global options, as well as `rsync` modules which will be served by the rsync daemon. To learn more details, see **`man rsyncd.conf**.` A simple example of this is the following:

```bash
port = 873
address = 192.168.122.11

[mymodule]
    comment = Default new module, made by me
    path = /srv/rsync/mymodule
```

**rsync Daemon Security**

The **`rsync**` daemon introduces some security considerations:

- The **use chroot** option will change the daemon's root to the module's directory, reducing the impact of security bugs.
- The **list** option will modify the visibility of your module in the output of a remote listing.
- The **filter**, **exclude**, **include**, **exclude from**, and **include from** options restrict or enable access to files and/or patterns.
- The **hosts allow**, and **hosts deny** options control which IP addresses can connect to the server.
- The **read only** option (the default) disallows any write access. If **read only** is false, the filesystem permissions determine the access the rsync daemon has.
- The **write only** option will disallow read access, turning the module into a "dead drop".

For more details see **man 5 hosts_access**.

When running the **rsync** command, it will use any transparent remote shell. The default shell is ssh, which encrypts the traffic.

Advanced rsync Usage

Advanced **rsync** usage provides a time machine backup, syncs only files which exist in the destination and deletes them, while backing them up.

The following command below is a time machine-like backup solution:

```bash
DATE=$(date "+%FT%T")
rsync -aP --link-dest=/srv/backup/current /things/to/backup /srv/backup/backup-\DATE
rm -f /srv/backup/current
ln -s /srv/backup/backup-$DATE /srv/backup/current
```

To only copy files which already exist in the destination folder, do this command:

```bash
$ rsync -r --existing /some/src/. /some/dest/.
```

To delete items which do not exist on the source, but keep a backup, do this command:

```bash
$ rsync -r --backup --backup-dir=backup-$(DATE +%Y.%m.%d) --delete /some/src/./some/dest/.
```

# **scp and sftp**

**`scp**` (Secure Copy) is non-interactive, while **`sftp**` (Simple File Transfer Protocol) is interactive.

An example of copying a file using scp is the following command:

```bash
$ scp root@server:/etc/hosts /tmp/server.hosts
```

An example of copying a file using sftp is the following command:

```bash
$ sftp root@server:/etc/
sftp> get hosts
```

The output from this command shows:

```bash
Fetching /etc/hosts to hosts
/etc/hosts
```

**`sftp> quit`**

The commands **scp**
 and **sftp**
 will use your system or the user ssh client configuration files `(**~/.ssh/config**)`.

# **rsync over SSH**

**`rsync**` over SSH has the same command options as the stand-alone **`rsync`**. **`rsync**` uses your ssh configuration.

The **`rsync**` command will default to using the SSH shell, unless you specify an alternate shell. The rsync protocol can be invoked with the **`rsync://`** URI in the command or with **`rsyncd`**.

To copy an entire directory using rsync over ssh, use the following command:

**`$ rsync -av root@server:/etc/. /srv/backup/server-backup/etc/.`**

# ****WebDAV****

What Is WebDAV?

WebDAV is an extension to HTTP for read/write access to resources and is available on most operating systems.

Windows, Linux, and Mac OSX have native support for WebDAV. Also, a command-line tool **`cadaver**` can be used to manipulate a WebDAV share. The Apache module **`mod_dav**` is one method to enable WebDAV.

You can enable WebDAV with a stanza like this:

```bash
Alias /mydav/ /srv/mydav/
<Directory /srv/mydav/>
    Options +Indexes
    Order Allow,Deny
    Allow from all
    Dav On

    AuthType Basic
    AuthName DAV
    AuthUserFile mydav.passwd

    <LimitExcept GET OPTIONS>
        Require user admin
    </LimitExcept>
</Directory>
```

**WebDAV Configuration**

**`mod_dav**` requires a defined lock file, which should be writable by the Apache user:

**`DavLockDB davlockdb`**

WebDAV can be enabled in either a **`<Directory>`** or **`<Location>`** stanza. However, it is more secure to create an alias and use the directory options to increase security.

You should not enable WebDAV until you have secured your web server. Uploads, PUT, POST, and similar methods should not be allowed, except for by an authenticated user. Using plain-text authentication is also frowned upon. You can use Digest, or SSL to encrypt your WebDAV session.

# **BitTorrent**

BitTorrent is a protocol which allows for very efficient transfer of large files or directories (e.g. ISO files) using a distributed peer-to-peer architecture. A file served over BitTorrent is divided into small chunks and is distributed by anyone who is connected to the same tracker. This allows for much lesser resource use by the server hosting the original content, as the file needs to be downloaded only once. Also, BitTorrent becomes more efficient the more concurrent clients are participating.

**Ktorrent**
GUI client for KDE.

**rtorrent**
CLI client for Linux.

**Transmission**
Client for Windows, Linux, and Mac OSX.

mktorrent

CLI tool for creating `.tor**rent`** files.