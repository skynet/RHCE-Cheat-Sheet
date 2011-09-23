# RHCE "Cheat Sheet"

This document attempts to provide answers to all study points on the [RHCE and RHCT Exam Preparation Guide](http://www.redhat.com/certification/rhce/prep_guide/) in a single-page (and thus, printable) format. This is **not** a "brain dump" or an attempt to cheat the [RH302](https://www.redhat.com/courses/rh302_rhce_exam/) exam in any way. These are just my self-study notes. Use them at your own risk.

* **Note:** Study points last updated on 2009-08-11. This list may become out of date without notice (especially after I pass the test ;-)).

## Other People's "Cheat Sheets"

The following links may have updated/additional information, but I am not responsible for any of it. If you are a Red Hat employee, and you believe that I've linked to something that violates the Red Hat NDA, please let me know, and I'll remove the link immediately.

* [Dino Conti's RHCE Cheat Sheet](http://mail.dclinux.com/wiki/index.php/RHCE_Cheat_Sheet)

## Testing Environment with Sun VirtualBox

install guest additions:

    yum install gcc kernel-devel
    sh /media/VBOXADDITIONS*/VBoxLinuxAdditions-x86.run
    reboot

## Prerequisite skills for RHCT and RHCE

Candidates should possess the following skills, as they may be necessary in order to fulfill requirements of the RHCT and RHCE exams:

### use standard command line tools (e.g., ls, cp, mv, rm, tail, cat, etc.) to create, remove, view, and investigate files and directories

### use grep, sed, and awk to process text streams and files

### use a terminal-based text editor, such as vim or nano, to modify text files

### use input/output redirection

<table>
    <tr>
        <td>operator</td>
        <td>description</td>
    </tr>
    <tr>
        <td>&gt;</td>
        <td>redirect STDOUT to a file</td>
    </tr>
    <tr>
        <td>2&gt;</td>
        <td>redirect STDERR to a file</td>
    </tr>
    <tr>
        <td>&amp;&gt;</td>
        <td>redirect all output to a file</td>
    </tr>
    <tr>
        <td>2&gt;&amp;1</td>
        <td>redirect all output to a pipe</td>
    </tr>
</table>

* use **>>** to append instead of overwrite

### understand basic principles of TCP/IP networking, including IP addresses, netmasks, and gateways for IPv4 and IPv6

### use su to switch user accounts

    su - <user>

### use passwd to set passwords

    passwd <user>

### use tar, gzip, and bzip2

    # compress (tar/gzip)
    tar cvzf <file>.tgz <directory>

    # extract (tar/gzip)
    tar xvzf <file>.tgz

    # compress (tar/bzip)
    tar cvjf <file>.tbz <directory>

    # extract (tar/bzip)
    tar xvjf <file>.tbz


### configure an email client on Red Hat Enterprise Linux

    echo "message" | mail <email> -s "subject"
    mail <email> -s "subject" < <file>

### use text and/or graphical browser to access HTTP/HTTPS URLs

* elinks
* lynx

### use lftp to access FTP URLs

## RHCT skills

### Troubleshooting and System Maintenance

RHCTs should be able to:

#### boot systems into different run levels for troubleshooting and system maintenance

append the desired runlevel to grub's kernel line:

* **1-5** runs appropriate rc and init scripts
* **single** only runs rc.sysinit
* **emergency** skips all rc and init scripts

#### diagnose and correct misconfigured networking

1. check **/etc/sysconfig/network**
1. check **/etc/sysconfig/network-scripts/ifcfg-<interface>**
1. service network restart
1. chkconfig network on
1. ifconfig
1. ping <localhost ip>
1. netstat -r
1. ping <default gateway>
1. ping 4.2.2.2

redhat network config tool:

    system-config-network

#### diagnose and correct hostname resolution problems

1. check **/etc/nsswitch.conf**
1. check **/etc/resolv.conf**
1. check **/etc/hosts**
1. dig @<dns server> google.com

redhat network config tool:

    system-config-network

#### configure the X Window System and a desktop environment

install x:

    yum groupinstall "x window system"

* init respawns **/etc/X11/prefdm -nodaemon** to keep x running in runlevel 5
* **startx** to start manually

xfs is supposedly required for x windows (even though i can run x fine without it...):

    service xfs on
    chkconfig xfs on

x environment config:

* /etc/sysconfig/desktop
* /etc/X11/xinit/xinitrc
* /etc/X11/xinit/Xclients
* ~/.xinitrc
* ~./Xclients

redhat display config tool:

    system-config-display [--reconfig]

install gnome desktop:

    yum groupinstall "gnome desktop environment"

switchdesk allows you to change your desktop environment:

    yum install switchdesk
    switchdesk

if switchdesk is not available, edit **/etc/sysconfig/desktop**:

    DISPLAYMANAGER=<GNOME|KDE|XDM>
    DESKTOP=<GNOME|KDE>

#### add new partitions, filesystems, and swap to existing systems

##### partitions

manage partitions:

    fdisk <device>
    partprobe

##### filesystems

make filesystems:

    mkfs.<ext2|ext3>

label filesystems:

    e2label <partition> <label>
    blkid

manage filesystem settings:

    tune2fs <partition>
    dumpe2fs <partition>

##### swap

note that it's possible to create a swap **file** instead of a partition:

    dd if=/dev/zero of=<file> bs=1024 count=<size>

format the file/partition:

    mkswap <partition|file>
    nano -w /etc/fstab
    swapon -va
    cat /proc/swaps

#### use standard command-line tools to analyze problems and configure system

* check for full filesystems, quotas

### Installation and Configuration

RHCTs must be able to:

#### perform network OS installation

at boot prompt:

    linux askmethod

#### implement a custom partitioning scheme

#### configure printing

printing support is provided by cups:

    service cups start
    chkconfig cups on

redhat printer config tool:

    system-config-printer

web config tool:

    http://localhost:631

printing via command line:

    # print
    lpr <file>
    # view print queue
    lpq
    # remove print job
    lprm <job number>

#### configure the scheduling of tasks using cron and at

##### cron

make sure vixie cron is installed and running:

    yum install vixie-cron
    service crond start
    chkconfig crond on

1. if **/etc/cron.allow** exists, only these users are allowed (**/etc/cron.deny** is ignored)
1. if **/etc/cron.allow** does not exist, everyone allowed except users in **/etc/cron.deny**
1. if neither exists, only root allowed
1. empty **/etc/cron.deny** means all users allowed (default)

edit your cron jobs:

    crontab -e

crontab format:

    <minute> <hour> <day of month> <month> <day of week> <command>

**Note:** **/etc/crontab** has additional **user** field before command.

##### at/batch

make sure at is installed and running:

    yum install at
    service atd start
    chkconfig atd on

1. if **/etc/at.allow** exists, only these users are allowed (**/etc/at.deny** is ignored)
1. if **/etc/at.allow** does not exist, everyone allowed except users in **/etc/at.deny**
1. if neither exists, only root allowed
1. empty **/etc/at.deny** means all users allowed (default)

example session:

    # add jobs
    at now + 1 hour
    at> <command>

    at 09:00 2009-07-23
    at> <command>

    batch
    at> <command>

    # list jobs
    atq

    remove jobs
    atrm <job>

#### attach system to a network directory service, such as NIS or LDAP

redhat config tools:

    system-config-authentication
    authconfig-tui

required packages for nis:

    yum install ypbind portmap

required packages for ldap:

    yum install nss-ldap openldap

#### configure autofs

make sure the autofs service is running:

    service autofs start
    chkconfig autofs on

ensure the following line in **/etc/nsswitch.conf**:

    automount: files nis

define an autofs-controlled mountpoint called **test** by adding the following to **/etc/auto.master**:

    /test /etc/auto.test

create **/etc/auto.test**:

    blah example.com:/pub/something
    * example:/home/&

1. local **/test/blah** => remote **example.com:/pub/something**
1. local **/test/user** => remote **example:/home/user** (**Note:** this method can be used to automount home directories)

test automounting:

    ls /test/blah
    ls /test/user

    # redhat defaults
    ls /net/<hostname>
    ls /misc/cd

#### add and manage users, groups, quotas, and File Access Control Lists

redhat user/group config tool:

    system-config-users

##### users

**/etc/passwd** file format:

    username:password:uid:gid:gecos:homedir:shell

**/etc/shadow** file format:

    username:password:lastpwchange:minpwchange:maxpwage:pwchangewarn:inactive:expire

command line user management:

    useradd <user>
    usermod <user>
    chage <user>
    userdel <user>
    pwck

* default account expiration settings in **/etc/login.defs**

##### groups

**/etc/group** file format:

    groupname:password:gid:members

command line group management:

    groups <user>
    groupadd <user>
    groupmod <user>
    groupdel <user>
    grpck

##### quotas

install quota package

    yum install quota

add fs options to **/etc/fstab**:

    usrquota,grpquota

remount device

    mount -o remount <mount point>

init quota database:

    quotacheck -cugm <device>

enable/disable quotas

    quotaon <device>
    quotaoff <device>

edit quotas

    edquota -u <user>
    edquota -g <group>

edit grace time

    edquota -ut <user>
    edquota -gt <group>

check/report quotas

    quota <user>
    repquota -aug

##### Access Control Lists

install acl package

    yum install acl

add fs options to **/etc/fstab**:

    acl

remount device:

    mount -o remount <mount point>

manage acls:

    # set acls
    setfacl -m [d:]u:<user>:<r|w|x|-> <file>
    setfacl -m [d:]g:<group>:<r|w|x|-> <file>

    # get acls
    getfacl <file>

    # remove acls
    setfacl -x u:<user> <file>
    setfacl -x g:<user> <file>
    setfacl --remove-all <file>
    setfacl --remove-default <file>

#### configure filesystem permissions for collaboration

1. create new group
1. add users to group
1. chown folder to root.<group>
1. chmod folder to 2770 (g+s)

#### install and update packages using rpm

    # install
    rpm -ivh <package>.rpm

    # update
    rpm -Uvh <package>.rpm

    # freshen
    rpm -Fvh <package>.rpm

    # remove
    rpm -e <package>

    # query by file name
    rpm -qf <full path of file>

    # verify a file
    rpm -Vf > <full path of file>

    # verify status of all packages
    rpm -Va > /tmp/rpmverify

**Note:** while inside the rescue environment, use the --root option to specify the **real** location of your root file system (e.g. --root=/mnt/sysimage).

#### properly update the kernel package

1. **always** do an install (i.e. rpm -ivh <kernel package>) rather than an update
1. check **/boot/grub/grub.conf** for proper configuration

#### configure the system to update/install packages from remote repositories using yum or pup

yum config goes in **/etc/yum.repos.d/**

    [id]
    name=my repo
    baseurl=http://example.com/centos/
    enabled=1

#### modify the system bootloader

* production config is in **/boot/grub/grub.conf**
* see examples in **/usr/share/doc/grub-*/menu.lst**

#### implement software RAID at install-time and run-time

to start, we need at least two devices/partitions of type "linux raid autodetect" (use fdisk to set partition type to "fd")

create raid device:

    mdadm --create /dev/md0 --level=<0|1|4|5|6|10> --raid-devices=<num> <device list>

fail disk in array:

    mdadm /dev/md0 -f <device>

remove disk from array:

    mdadm /dev/md0 -r <device>

add disk to array:

    mdadm /dev/md0 -a <device>

stop array:

    mdadm --stop /dev/md0

check raid status:

    mdadm --detail /dev/md0

    cat /proc/mdstat

format works as usual:

    mkfs.ext3 /dev/md0

**Note:** don't forget to configure **/etc/fstab** appropriately.

#### use /proc/sys and sysctl to modify and set kernel run-time parameters

config is in **/etc/sysctl.conf**

    # search through parameters
    sysctl -a | grep <whatever>
    # apply changes from config file immediately
    sysctl -p

#### use scripting to automate system maintenance tasks

#### configure NTP for time synchronization with a higher-stratum server

redhat config tool:

    system-config-date

* config is in **/etc/ntp.conf**

synchronization configuration example:

    server 0.pool.ntp.org
    server 1.pool.ntp.org
    server 2.pool.ntp.org

apply changes:

    service ntpd restart
    chkconfig ntpd on

verify changes:

    ntpq -p

##### RHCE skills

### Troubleshooting and System Maintenance

RHCEs must demonstrate the RHCT skills listed above, and should be able to:

#### use the rescue environment provided by first installation CD

    linux rescue

* when working in non-chrooted rescue mode:
* mount /dev/hdc /mnt/source (to access install files on the cd/dvd)
* rpm commands should use the **--root=/mnt/sysimage** option

manually make /dev and /proc available in chrooted mode:

    mount -o bind /dev /mnt/sysimage/dev
    mount -o bind /proc /mnt/sysimage/proc

chroot /mnt/sysimage

#### diagnose and correct boot failures arising from bootloader, module, and filesystem errors

check in order:
1. mbr
1. /boot/grub/grub.conf
1. /etc/fstab
1. /etc/inittab
1. /etc/rc.d/rc.sysinit
1. /etc/rc.d/rc*.d
1. /etc/rc.d/init.d/*
1. /etc/rc.d/rc.local

##### grub errors

* in general, use the last line before the error message to see where grub error'd out
* to find correct value for **root** option, type **find /grub/stage1** at the grub command line (**Note:** remember that all file names in **grub.conf** are relative to the **root** option)
* check for missing files in kernel and/or initrd lines

##### kernel errors

* missing/corrupt initrd file results in: **kernel panic - not syncing: vfs: unable to mount root fs on unknown-block**
* invalid **root** parameter for kernel results in: **setuproot: error mounting /proc: No such file or directory**

##### other

reinstall grub to mbr:

    grub-install <device>

recreate initrd:

    mkinitrd <filename> <kernel version>

fix corrupt filesystem:

    fsck <partition>

if fsck is unable to locate a superblock, you can specify an alternative one:

    dumpe2fs <partition>
    fsck -b <block#> <partition>

#### diagnose and correct problems with network services (see Installation and Configuration below for a list of these services)

see what's listening on what port:

    netstat -ntaupe

#### add, remove, and resize logical volumes

redhat lvm config tool:

    yum install system-config-lvm
    system-config-lvm

create physical volume:

    pvcreate <device>

create volume group:

    vgcreate <name> <pv device> [pv device]

extend volume group:

    vgextend <name> <pv device>

create logical volume:

    lvcreate --size <size>M --name <lv name> <vg name>

extend logical volume:

    lvextend --size <size>M <device>
    resize2fs <device>

shrink logical volume:

    resize2fs <device> <size>M
    lvreduce --size <size>M <device>

remove logical volume:

    lvremove <device>

#### diagnose and correct networking services problems where SELinux contexts are interfering with proper operation.

enable/disable selinux in **/etc/sysconfig/selinux**:

    SELINUX=enforcing
    SELINUXTYPE=targeted

install selinux troubleshooter:

    yum install setroubleshoot
    service setroubleshoot start
    chkconfig setroubleshoot on

install selinux management tool:

    yum install policycoreutils-gui

list selinux errors:

    sealert -a /var/log/audit/audit.log | less

launch gui browser:

    sealert -b

list selinux booleans:

    getsebool -a

set selinux boolean:

    setsebool -P <boolean> = <0|1>

list security contexts:

    ls -Z <file>

change security contexts:

    # using reference (copy contexts from existing known-good file)
    chcon -R --reference <old file> <new file>

    # manual
    chcon -R -u <user> <file>
    chcon -R -t <type> <file>

### Installation and Configuration

RHCEs must demonstrate the RHCT-level skills listed above, and they must be capable of configuring the following network services. For each of these services, RHCEs must be able to:

* install the packages needed to provide the service
* configure SELinux to support the service
* configure the service to start when the system is booted
* configure the service for basic operation
* Configure host-based and user-based security for the service

#### HTTP/HTTPS

##### install

    yum install httpd mod_ssl

##### selinux

make new DocumentRoot match default DocumentRoot (**Note:** this applies to any directory that apache will serve files from):

    chcon -R --reference /var/www /www

##### start at boot

    chkconfig httpd on

##### basic config

requirements for ~user/ directories:

* **UserDir** directive
* **chmod 701** the user's home directory
* change security context on the user's **UserDir**

requirements for .htaccess file usage:

* **AllowOverride All** directive

requirements for name-based virtual hosts:

* **NameVirtualHost *:80** and **NameVirtualHost *:443** directives
* each virtual host requires appropriate **ServerName** and **ServerAlias** directives
* **Note:** a single virtual host cannot span multiple ports (i.e. 80 and 443).    two separate **VirtualHost *:<port>** sections are needed to do this.

self-signed ssl cert:

    cd /etc/pki/tls/certs
    rm localhost.crt
    make testcert

check virtual host config:

    httpd -D DUMP_VHOSTS

##### host-based security

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>80, 443</td>
    </tr>
</table>

hosts are allowed by default and must be explicitly denied:

    <Directory /var/www/html>
       Order deny,allow
       Deny from 192.168.0.0/255.255.255.0
       Deny from badguys.example.com
    </Directory>

hosts are denied by default and must be explicitly allowed:

    <Directory /var/www/html>
       Order allow,deny
       Allow from 192.168.0.0/255.255.255.0
       Allow from goodguys.example.com
    </Directory>

##### user-based security

create web password file:

    htpasswd -c /etc/httpd/webusers testuser1
    htpasswd /etc/httpd/webusers testuser2

create web group file (**/etc/httpd/webgroups**):

    testgroup: testuser1 testuser2

allow access by group:

    <Directory /var/www/html>
       AuthType Basic
       AuthName "top secret area"
       AuthUserFile /etc/httpd/webusers
       AuthGroupFile /etc/httpd/webgroups
       Require group testgroup
    </Directory>

##### verify service functionality

test http/https:

    elinks <http|https>://<hostname>/[path]

#### SMB

##### install

    yum install samba samba-client

##### selinux

allow samba to share home directories:

    setsebool -P samba_enable_home_dirs=1

mark a directory as sharable with samba:

    chcon -R -T samba_share_t <directory>

##### start at boot

    chkconfig smb on

##### basic config

redhat samba config tool:

    yum install system-config-samba
    system-config-samba

set workgroup/domain:

    workgroup = <workgroup>

security modes:

    # connections check local pwdb (default)
    security = user

    # member server on a domain, uses pwdb on a dc
    security = domain
    workgroup = EXAMPLE

    # member server on an ad domain using kerberos, uses pwdb on a dc
    security = ads
    realm = EXAMPLE.COM
    password server = kerberos.example.com

    # used when samba was not capable of being a domain member server (DO NOT USE)
    security = server
    encrypt passwords = yes
    password server = <netbios name of dc>

    # each share requires a password (DO NOT USE)
    security = share

share options:

    [<share name>]
    # path for share
    path = <path>

    # share is visible
    browseable = <yes|no>

    # rw enabled
    writeable = <yes|no>

    # this is a shared printer
    printable = <yes|no>

    # all users connecting to this share use <group> as their primary group
    group = <group name>

join domain:

    net rpc join -U root

fstab example:

    //<hostname>/<share> <mountpoint> cifs user=<username>,pass=<password> 0 0

**Note:** **mount.cifs** and **umount.cifs** need to be chmod'ed u+s in order to be used by non-root users

##### host-based security

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>139, 445</td>
    </tr>
    <tr>
        <td>udp</td>
        <td>137, 138</td>
    </tr>
</table>

hosts allow/deny can be used per-server or per-share:

    hosts allow = 127.0.0.1 192.168.2.0/24 192.168.3.0/24
    hosts deny = 0.0.0.0/0

##### user-based security

account maintenance:

    # add account (local linux account must exist first, or be translated via /etc/samba/smbusers):
    smbpasswd -a <username>

    # enable/disable account:
    smbpasswd -e <username>
    smbpasswd -d <username>

    # remove account:
    smbpasswd -x <username>

**Note:** **service smb reload** may be needed after account changes

share access:

    valid users = <user1> @<group1>

* share access is also controlled by unix file permissions

##### verify service functionality

list shares:

    smbclient -L <hostname> -U <username>

browse shares:

    smbclient //<hostname>/<share> -U <username>

test allow/deny statements for a host:

    testparm /etc/samba/smb.conf <hostname> <ip address>

#### NFS

##### install

    yum install portmap nfs-utils

##### start at boot

    chkconfig portmap on
    chkconfig nfs on
    chkconfig nfslock on
    chkconfig netfs on

##### basic config

redhat config tool:

    yum install system-config-nfs
    system-config-nfs

format of **/etc/exports**:

    <mountpoint> <host>(<options>) [<host>(<options>) ...]

activate new exports:

    /etc/init.d/nfs restart

##### host-based security

**Note:** edit **/etc/sysconfig/nfs** and restart nfs to set static ports

firewall config:

    # see ports
    rpcinfo -p

host based security is intrinsic to the format of the exports file

##### user-based security

use standard file permissions

##### verify service functionality

list exports:

    showmount -e <host>

#### FTP

##### install

    yum install vsftpd

##### selinux

allow local users to log in and cd into home directories:

    setsebool -P ftp_home_dir=1

##### start at boot

    chkconfig vsftpd on

##### basic config

##### host-based security

* use ipchains with **-[!]s** option

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>21</td>
    </tr>
</table>

**Note:** ftp data transfers will not work unless **ip_conntrack_ftp** is added to **IPTABLES_MODULES** in **/etc/sysconfig/iptables-config**

tcp_wrappers example:

    vsftpd : 192.168.0.

##### user-based security

* allow/deny controlled via **/etc/vsftpd/user_list** (**Note:** users in **/etc/vsftpd/ftpusers** are always denied via pam)
* default allow/deny is configured by **userlist_deny** statement in **vsftpd.conf**

##### verify service functionality

test ftp:

    ftp <server>

#### Web proxy

##### install

    yum install squid

##### selinux

allow squid to connect to the network (this is recommended, but was not needed in my testing):

    setsebool -P squid_connect_any=1

##### start at boot

    chkconfig squid on

##### host-based security

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>3128</td>
    </tr>
</table>

allow access from local networks:

    acl our_networks src 192.168.1.0/24 192.168.2.0/23
    http_access allow our_networks

##### user-based security

FIXME

##### verify service functionality

test proxy:

    HTTP_PROXY=<server>:3128 elinks

#### SMTP

##### install

    yum install postfix
    alternatives --config mta
    service sendmail stop

##### start at boot

    chkconfig postfix on

##### basic config

listen on public interfaces:

    inet_interfaces = all

specify all destination hostnames/domains:

    mydestination = <hostname1>, <hostname2>, ...

specify origin domain:

    myorigin = $mydomain

local aliases in **/etc/aliases** (**Note:** dont forget to run **newaliases** to apply changes):

    <alias>: <user1>[, user2]

virtual aliases in **/etc/postfix/virtual** (**Note:** dont forget to run **postmap /etc/postfix/virtual** to apply changes):

    <virtual alias>: <user>

enable virtual aliases:

    virtual_alias_maps = hash:/etc/postfix/virtual

outbound address rewriting in **/etc/postfix/generic** (**Note:** dont forget to run **postmap /etc/postfix/generic** to apply changes):

    <outbound alias>: <user>

enable outbound aliases:

    smtp_generic_maps = hash:/etc/postfix/generic

##### host-based security

* use ipchains with **-[!]s** option

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>25</td>
    </tr>
</table>

##### user-based security

FIXME use smtp auth?

##### verify service functionality

test smtp:

    telnet <server> 25

#### IMAP, IMAPS, and POP3

##### install

    yum install dovecot

##### start at boot

    chkconfig dovecot on

##### basic config

enable protocols:

    protocols = <protocol list>

create custom ssl cert:

    nano -w /etc/pki/dovecot/dovecot-openssl.cnf
    /usr/share/doc/dovecot-*/examples/mkcert.sh
    service dovecot restart

##### host-based security

use ipchains with **-[!]s** option

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>143, 110, 995, 993</td>
    </tr>
</table>

##### user-based security

use pam_listfile in **/etc/pam.d/dovecot**

##### verify service functionality

test mailbox acess:

    mutt -f <imap|imaps|pop|pops>://<user>@<server>

#### SSH

##### install

    yum install openssh-server

##### start at boot

    chkconfig sshd on

##### user-based security

allow/deny user access:

    AllowUsers user1 user2 user3@example.com
    DenyUsers user4 user5 user6@example.com

##### host-based security

* use ipchains with **-[!]s** option

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>22</td>
    </tr>
</table>

tcp_wrappers example:

    sshd : 192.168.0.

##### verify service functionality

test logging in:

    ssh <user>@<server>

#### DNS (caching name server, slave name server)

##### install

    yum install bind-chroot caching-nameserver

##### start at boot

    chkconfig named on

##### basic config

copy sample config:

    cp -a /var/named/chroot/etc/named.caching-nameserver.conf /var/named/chroot/etc/named.conf\

caching-only nameserver:

* edit **listen-on** directives (comment out to listen on all interfaces)
* edit **allow-query** directives (comment out allow queries from everyone)
* edit **match-clients** and **match-destinations** directives to allow recursive queries from other hosts

slave nameserver:

* get slave example from **/usr/share/doc/bind-*/sample/etc/named.conf**

##### host-based security

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>tcp</td>
        <td>53</td>
    </tr>
    <tr>
        <td>udp</td>
        <td>53</td>
    </tr>
</table>

allow-query example:

    allow-query { 192.168.0.0/16; localnets; };

##### user-based security

N/A

##### verify service functionality

test query:

    dig @<server> <domain>

test zone transfer:

    dig @<server> <domain> axfr

#### NTP

##### install

    yum install ntp

##### start at boot

    chkconfig ntpd on

##### host-based security

firewall config:

<table>
    <tr>
        <td>protocol</td>
        <td>ports</td>
    </tr>
    <tr>
        <td>udp</td>
        <td>123</td>
    </tr>
</table>

allow other servers to sync with us:

    restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

##### user-based security

N/A

##### verify service functionality

show peers:

    ntpq -p

### RHCEs must also be able to:

#### configure hands-free installation using Kickstart

    yum install system-config-kickstart

1. make installation tree available
1. create kickstart file (use **system-config-kickstart** to create **ks.cfg**) and validate (using **ksvalidator**)
1. validate kickstart file
1. make kickstart file available
  * bootable diskette (place in top level directory)
  * bootable cdrom (place in top level directory)
  * network (http, ftp, nfs)
1. use bootable media and supply appropriate kernel parameter

    ks=floppy:/ks.cfg
    ks=cdrom:/ks.cfg
    ks=http://example.com/ks.cfg
    ks=nfs:example.com:/ks.cfg

#### implement logical volumes at install-time

#### use iptables to implement packet filtering and/or NAT

**Note:** do **not** use system-config-securitylevel, as it will overwrite your custom iptables rules. the following method seems to be the best way to go:

1. make changes in **/etc/sysconfig/iptables**
1. run **/etc/init.d/iptables restart** to apply changes

##### packet filtering

packet filtering example:

    -A <chain> -p <tcp/udp> -m <tcp/udp> [-s[!] <source address>] --dport <destination port> -j ACCEPT

##### NAT

enable ip forwarding in **/etc/sysctl.conf**:

    net.ipv4.ip_forward = 1

to test from another machine:

    ip route replace default via <ip address>

to make nat changes permanent, add the following to the top of **/etc/sysctl.conf**

    *nat
    :PREROUTING ACCEPT [0:0]
    :POSTROUTING ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    # nat rules go here
    COMMIT

inbound dnat:

    iptables -t nat -A PREROUTING -p <tcp/udp> --dport <destination port> -j DNAT --to-dest <private server>:<port>

outbound dnat:

    iptables -t nat -A OUTPUT -p <tcp/udp> --dport <destination port> -j DNAT --to-dest <private server>:<port>

masquerading:

    iptables -t nat -A POSTROUTING -o <outbound interface> -j MASQUERADE

snat:

    iptables -t nat -A POSTROUTING -j SNAT --to-source <public server>:<port>

example for forwarding port 80 requests to port 8080:

    iptables -A PREROUTING -i eth+ -p tcp --dport 80 -j REDIRECT --to-port 8080

FIXME

#### use PAM to implement user-level restrictions

##### module documentation

* **/usr/share/doc/pam-*/txts**

##### module configuration

* **/etc/pam.d**
* **/etc/security**

    <module interface> <control flag> <module name> <module arguments>

<table>
    <tr>
        <td>interface</td>
        <td>description</td>
    </tr>
    <tr>
        <td>auth</td>
        <td>user authentication (e.g. verifies password, set group membership or kerberos tickets, etc.)</td>
    </tr>
    <tr>
        <td>account</td>
        <td>verifies that access is allowed (e.g. expired account?, check group membership, etc.)</td>
    </tr>
    <tr>
        <td>password</td>
        <td>handles password changes</td>
    </tr>
    <tr>
        <td>session</td>
        <td>manages user sessions (e.g. mount home dir, create mailbox, logging, etc.)</td>
    </tr>
</table>

<table>
    <tr>
        <td>control flag</td>
        <td>description</td>
    </tr>
    <tr>
        <td>required</td>
        <td>must pass, **continue** testing on failure</td>
    </tr>
    <tr>
        <td>requisite</td>
        <td>must pass, **stop** testing on failure</td>
    </tr>
    <tr>
        <td>sufficient</td>
        <td>failure is ignored, but if passing so far, return success at this point</td>
    </tr>
    <tr>
        <td>optional</td>
        <td>pass or failure is irrelevant</td>
    </tr>
    <tr>
        <td>include</td>
        <td>include another file</td>
    </tr>
</table>

##### pam_listfile.so example

allow/deny users if listed in **/etc/special**:

    auth required pam_listfile.so onerr=success item=user sense=<allow|deny> file=/etc/special

##### Additional Notes

### tcp_wrappers

file format:

    <daemon list> : <client list> [except <client list>] [: <option>]

search order:

1. /etc/hosts.allow
1. /etc/hosts.deny
1. allow by default

**Note:** searching stops on first match

### Troubleshooting

#### unable to log in

* password wrong or expired?
* account locked?
* shell set to **/sbin/nologin**, **/bin/false**, etc.?
* root user and **PermitRootLogin no** in **/etc/ssh/sshd_config**?
* root user and terminal not listed in **/etc/securetty**?
* non-root user and **/etc/nologin** exists?
* check pam_listfile restrictions
