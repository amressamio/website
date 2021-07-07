---
title: "NSD"
linkTitle: "NSD"
weight: 2
description: >
  The NLnet Labs Name Server Daemon (NSD).
---
<!--
{{% pageinfo %}}
This is a placeholder page that shows you how to use this template site.
{{% /pageinfo %}}
-->

The NLnet Labs Name Server Daemon (NSD) is an authoritative DNS name server. It has been developed for operations in environments where speed, reliability, stability and security are of high importance.

NSD has a pure design philosophy that prioritises raw performance. This means that if you serve hundreds of thousands or even millions of queries per second, NSD is the leading implementation in the world. This makes the name server ideally suited for Top Level Domain implementations, DNS Root servers and anyone in need of a fast and optimised authoritative name server. Currently, three DNS root servers and many top-level domain registries use NSD as part of their server implementation. NSD has not implemented recursive caching by design. If you need a validating, recursive, caching resolver then NLnet Labs has [Unbound](https://www.nlnetlabs.nl/projects/unbound/about/) available.

### Let's install it
```html
# pkg install nsd
# sysrc nsd_enable="YES"
```
### NSD Control Setup
```html
# nsd-control-setup
```
### Secret Key
Create a secret key for TSIGs that secure zone transfers. and add it to *nsd.conf*.
```html
$ dd if=/dev/random of=/dev/stdout count=1 bs=32 | b64encode amressam
```
### /usr/local/etc/nsd.conf
```html
server:
	# Number of NSD servers to fork.  Put the number of CPUs to use here.
	 server-count: 2

	# Specify specific interfaces to bind (default are the wildcard
	 ip-address: 192.168.100.249

	# listen on IPv4 connections
	 do-ip4: yes

	# listen on IPv6 connections
	 do-ip6: no

	# port to answer queries on. default is 53.
	 port: 53

	# Verbosity level.
	 verbosity: 0

	# After binding socket, drop user privileges.
	# can be a username, id or id.gid.
	 username: nsd

	# The directory for zonefile: files.  The daemon chdirs here.
	 zonesdir: "/usr/local/etc/nsd"

	# the list of dynamically added zones.
	 zonelistfile: "/var/db/nsd/zone.list"

	# the database to use
	# if set to "" then no disk-database is used, less memory usage.
	 database: "/var/db/nsd/nsd.db"

	# log messages to file. Default to stderr and syslog (with
	# facility LOG_DAEMON).  stderr disappears when daemon goes to bg.
	 logfile: "/var/log/nsd.log"

	# File to store pid for nsd in.
	 pidfile: "/var/run/nsd/nsd.pid"

# Remote control config section. 
remote-control:
	# Enable remote control with nsd-control(8) here.
	# set up the keys and certificates with nsd-control-setup.
	 control-enable: yes

	# what interfaces are listened to for control, default is on localhost.
	# with an absolute path, a unix local named pipe is used for control
	# (and key and cert files are not needed, use directory permissions).
	 control-interface: 127.0.0.1
	# control-interface: ::1

	# port number for remote control operations (uses TLS over TCP).
	 control-port: 8952

	# nsd server key file for remote control.
	 server-key-file: "/usr/local/etc/nsd/nsd_server.key"

	# nsd server certificate file for remote control.
	 server-cert-file: "/usr/local/etc/nsd/nsd_server.pem"

	# nsd-control key file.
	 control-key-file: "/usr/local/etc/nsd/nsd_control.key"

	# nsd-control certificate file.
	 control-cert-file: "/usr/local/etc/nsd/nsd_control.pem"


# Secret keys for TSIGs that secure zone transfers.
# You could include: "secret.keys" and put the 'key:' statements in there,
# and give that file special access control permissions.
#
key:
	# The key name is sent to the other party, it must be the same
	 name: "key"
	# algorithm hmac-md5, or sha1, sha256, sha224, sha384, sha512
	 algorithm: sha256
	# secret material, must be the same as the other party uses.
	# base64 encoded random number.
	# e.g. from dd if=/dev/random of=/dev/stdout count=1 bs=32 | base64
	 secret: "ZcsLXe1iE/BpMA+qm7FojZKyaYTHfF1bd7BYsaOoD0k="


# Fixed zone entries.  Here you can config zones that cannot be deleted.
# Zones that are dynamically added and deleted are put in the zonelist file.
#
zone:
	 name: "amressam.io"
	# you can give a pattern here, all the settings from that pattern
	# are then inserted at this point
	# include-pattern: "master"
	# You can also specify (additional) options directly for this zone.
	 zonefile: "amressam.io.zone"
	# request-xfr: 192.0.2.1 example.com.key
```

### /usr/local/etc/nsd/amressam.io.zone
```html
$ORIGIN amressam.io.
$TTL 3600
@       IN      SOA     pns.amressam.io.     amr.amressam.io.     (
                        20111111        ; Serial
                        3600            ; Refresh
                        600             ; Retry
                        86400           ; Expire
                        600 )           ; Negative Cache TTL

        IN      NS      pns.amressam.io.
        MX      10      pmx.amressam.io.
        IN      TXT     "v=spf1 a mx -all"

dkim._domainkey.amressam.io.         IN      TXT     ("v=DKIM1;k=rsa;p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA6J6hlf8NJOPWy2trVRPg6SaiUtseQPlc3CcyiCRNIEl0gFAsDGIWu25hlMjOSk7DLSlzz9jU"
"pr24HSix8cLcbZW3LZbk6CXaJB8WEPEjVrLWHvLIf4zdaBSWt5acx9EgkRBlynyk0pQ9+qa+khBkPdwtfImjf+yZq0n69x6LOw/pgH96c1zOquOblzylfMAHFCEyEa0YqCx8pBppNkJQ7JUxzR0Sy95Ds3PfUIYom68vuYDD8Cv0kbL5hnJcjfXU+x02R")
_dmarc.amressam.io.                  IN      TXT     "v=DMARC1;p=reject;pct=100;rua=mailto:amr@amressam.io;"
_acme-challenge.amressam.io.         IN      TXT     "RAhdVu9Sji11fB-QxATQW4FtIh3-hahMY2h-CSXim0o"

@       IN      A       192.168.100.100
pns     IN      A       192.168.100.100
pmx     IN      A       192.168.100.150
```

### Start NSD
```html
# service nsd start
```