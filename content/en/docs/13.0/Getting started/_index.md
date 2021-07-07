---
title: "Getting Started"
linkTitle: "Getting Started"
weight: 2
description: >
  FreeBSD&trade; 13.0-RELEASE Installation
---
<!--
{{% pageinfo %}}
This is a placeholder page that shows you how to use this template site.
{{% /pageinfo %}}
-->

Installing FreeBSD&trade; 13.0-RELEASE is very easy, first thing we should do is to know where to install
the FreeBSD&trade; OS, whether to install it on a virtual machine or a bare metal.

### Installing FreeBSD&trade; on a virtual machine

You can use Oracle&trade; VM VirtualBox&trade;, VMware&reg; Workstation Player, Xen or KVM depends on your current OS, since I run everything on FreeBSD&trade; I will be using bhyve, the "BSD hypervisor" and [CBSD](https://cbsd.io) to show you how to install FreeBSD&trade;.

### Installing FreeBSD&trade; on a bare metal

First visit [FreeBSD&trade; website](https://www.freebsd.org/where/) and download FreeBSD&trade; image and burn it to the media associated with its file type and size (CD, DVD, or USB), if you are using Windows&reg; download [Rufus](https://rufus.ie) and if you are using Linux&reg; or Mac OS&reg; open your terminal and use dd command like the following example.

{{% alert title="Warning" color="warning" %}}
There is no undo with dd command, make sure to replace *of=/dev/da0* with the right device.
{{% /alert %}}

```html
# dd if=FreeBSD-13.0-RELEASE-amd64-memstick.img of=/dev/da0 bs=1M conv=fdatasync status=progress
```

Boot the system to install from the inserted media.
<!--
<iframe width="560" height="315" src="https://www.youtube.com/embed/NcJWygUjlcI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
-->
