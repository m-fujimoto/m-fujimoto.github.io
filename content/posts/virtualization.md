+++
date = '2025-10-30T17:11:55+09:00'
draft = true
title = 'Virtualization'
categories = ['OpenBSD']
tags = ['vm','pf.conf']
+++

## Prerequisites

Processor compatibility can be checked with the following command: 
```
$ dmesg | egrep '(VMX/EPT|SVM/RVI)'
vmm0 at mainbus0: VMX/EPT
vmm0 at mainbus0: VMX/EPT
```

Before going further, enable and start the vmd(8) service. 
```
$ doas rcctl enable vmd
$ doas rcctl start vmd
```

## Starting a VM

``` 
$ doas vmctl create -s 50G OpenBSD.img
vmctl: raw imagefile created
$ doas vmctl start -m 1G -L -i 1 -r install78.iso -d OpenBSD.img OpenBSD
vmctl: started vm 1 successfully, tty /dev/ttyp3
```

```
$ vmctl show
   ID   PID VCPUS  MAXMEM  CURMEM     TTY        OWNER    STATE NAME
    1 17157     1    1.0G    179M   ttyp3         root  running OpenBSD

```

```
$ doas vmctl console OpenBSD
Connected to /dev/ttyp3 (speed 115200)
```

Press Enter to begin the installation.

## Networking


### IP Forwarding

```
# sysctl net.inet.ip.forwarding=1
# echo  'net.inet.ip.forwarding=1' >> /etc/sysctl.conf
```

### pf.conf

```
#       $OpenBSD: pf.conf,v 1.4 2018/07/10 19:28:35 henning Exp $
#
# See pf.conf(5) for syntax and examples.
# Remember to set net.inet.ip.forwarding=1 and/or net.inet6.ip6.forwarding=1
# in /etc/sysctl.conf if packets are to be forwarded between interfaces.

# increase default state limit from 100'000 states on busy systems
#set limit states 500000

set skip on lo

# filter rules and anchor for ftp-proxy(8)
#anchor "ftp-proxy/*"
#pass in quick inet proto tcp to port ftp divert-to 127.0.0.1 port 8021

# anchor for relayd(8)
#anchor "relayd/*"

block return    # block stateless traffic
pass            # establish keep-state

# rules for spamd(8)
#table <spamd-white> persist
#table <nospamd> persist file "/etc/mail/nospamd"
#pass in on egress inet proto tcp from any to any port smtp \
#    divert-to 127.0.0.1 port spamd
#pass in on egress proto tcp from <nospamd> to any port smtp
#pass in log on egress proto tcp from <spamd-white> to any port smtp
#pass out log on egress proto tcp to any port smtp


#block in quick from urpf-failed to any # use with care

dns_server = "8.8.8.8"

# rules for vmd(8) - NAT and DNS forwarding for VMs (100.64.0.0/10 default)
pass out on egress from 100.64.0.0/10 to any nat-to (egress)
pass in proto udp from 100.64.0.0/10 to any port domain \
    rdr-to $dns_server port domain

# By default, do not permit remote connections to X11
block return in on ! lo0 proto tcp to port 6000:6010
```

Create /etc/vm.conf if you need:

```
vm "OpenBSD" {
        memory 1G
        enable
        disk "/home/masayoshi/vm/OpenBSD.img"
        local interface
}
```

```
$ doas vmctl start OpenBSD
```