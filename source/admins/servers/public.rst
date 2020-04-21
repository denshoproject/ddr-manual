.. _guide:

===================
Public Server Setup
===================

This page documents the process of creating a Linux virtual machine to serve DDR repositories to the public.


Most of the commands you will type in the Linux virtual machine will be typed as `root`.  To become `root`, type the following at a command prompt::

    $ su - root
    [enter root password]

As per convention, variables in this documentation are written in upper case with underscores, starting with a "$" sign.

.. contents::
.. section-numbering::


Prepare For The Install
=======================



SSH Client and Terminal Emulator (optional)
-------------------------------------------

You may find it significantly easier to connect to your VM from your host OS via SSH, rather than interacting directly though the VM's window.

Windows:

* `PuTTY <https://duckduckgo.com/PuTTY>`_
* `Cygwin <http://cygwin.com/>`_

Mac OS X:

* Already has SSH which you can access via the built-in Terminal app.

Linux:

* If you're already using Linux you probably figure this out for yourself.



Install VirtualBox
------------------

Download VirtualBox from the official web site and install it.

- https://www.virtualbox.org/wiki/Downloads
- Look for "VirtualBox $VERSION_NUMBER for Windows hosts".
- Also download the "VirtualBox $VERSION_NUMBER Oracle VM VirtualBox Extension Pack".
- When all files are downloaded, launch the installer and install on your host machine.

.. note::
    During the installation process on Windows, you may encounter warning messages from Windows Firewall and/or AV security software. You should choose "Allow" or "OK" for all of these dialogs. 

Install the VirtualBox Extension Pack.

- Run VirtualBox.
- In the "VirtualBox Manager" window, click "File > Preferences" and select the "Extensions" tab.
- Click the "Add package" icon on the right side of the window.
- Locate the "VirtualBox $VERSION_NUMBER Oracle VM VirtualBox Extension Pack" that we downloaded earlier, then click "OK".



Host-only Network
-----------------

By default, VMs can only connect to the outside Internet.  VirtualBox's host-only network is used for communications between VMs and with your host OS.

- In the "VirtualBox Manager" window, click "File > Preferences", then select the "Network" tab.
- There should be a host-only network already created. Select it and click the screwdriver icon.
- Note details of Host-Only Network for later::

    Adapter
    - Network name: $HOST_ONLY_NETWORK_NAME
    - IPv4 Address: $HOST_ONLY_NETWORK_IPV4_ADDRESS (ex: 192.168.56.1)
    
    DHCP Server
    - Enable server: YES
    - Server Address: $HOST_ONLY_DHCP_SERVER_ADDRESS (ex: 192.168.56.100)
    - Server Mask: $HOST_ONLY_NETWORK_DHCP_SERVER_MASK (ex: 255.255.255.0)
    - Lower Address Bound: $HOST_ONLY_NETWORK_DHCP_LOWER_BOUND (ex: 192.168.56.101)
    - Upper Address Bound: $HOST_ONLY_NETWORK_DHCP_UPPER_BOUND (ex: 192.168.56.254)




Basic VM Setup
==============

The following steps are common to almost any good VM setup.  There's nothing here that is specific to DDR.



Create a new virtual machine
----------------------------

In the "VirtualBox Manager" window, click "Machine > New", then select the following options from the various screens that follow::

    Name and operating system
    - Name: ddrworkbench-$PARTNERCODE
    - Type: Linux
    - Version: Debian
    
    Memory Size
    - 2048 MB
    
    Hard Drive
    - Create a virtual hard drive now
    
    Hard drive file type
    - VMDK
    
    Storage on physical hard drive
    - Dynamically allocated
    
    File location and size
    - Same name as VM
    - 8.00 GB



Networking
----------

VMs do not automatically have access to the host-only network.  This step simulates plugging a network cable into your virtual machine and connecting it to the network.

- Right-click on the new VM, select Settings, then choose "Network" in the window.
- Confirm that Adapter 1 has the following settings, then enable and configure Adapter 2::

    Adapter 1
    - Enable Network Adapter: YES
    - Attached to: NAT
    - Leave all other settings untouched.
    
    Adapter 2
    - Enable Network Adapter: YES
    - Attached to: Host-only Adapter
    - Name: $HOST_ONLY_NETWORK_NAME
    - Leave all other settings untouched.



Shared Folders
--------------

The `ddr-public` virtual machine will take in media documents from your workstation, process them, and deposit them on a USB hard drive.
VirtualBox VMs can only access designated directories in the host filesystem.
In order for `ddr-public` to be able to ingest documents, you must designate a directory on your workstation into which you will deposit the documents.

- Right-click on the VM, select Settings, then choose "Shared Folders" in the window.
- Click "Add shared folder" icon on right side.
- Browse and select folder.
- Folder Name: "ddrshared"
  
  - Read-only: NO
  - Auto-mount: YES
  - Make Permanent: YES



Download Debian Stable ("Wheezy")
---------------------------------

- http://www.debian.org/distrib/
- Click on "32-bit PC netinst iso" to start the download.
- Note the location of the downloaded file: `$DEBIAN_ISO`.



Install Debian on the VM
------------------------

::

    Start the VM
    - Select VM from the list on the left.
    - Right-click and select "Start".
    
    Pop-up: Select start-up disk
    - Click browse icon. Locate and select $DEBIAN_ISO.
    
    Graphical Install
    
    Select a language
    - English
    
    Select your location
    - United States
    
    Configure the keyboard
    - American English
    
    Configure the network: Primary network interface
    - eth0
    
    Configure the network: Hostname
    - Enter your VM name
    
    Configure the network: Domain name
    - ddrpublic$NUM
    
    Set up users and passwords: Root password
    - ************ [choose a good password, write it down, keep in safe place]
    
    Set up users and passwords: Full name for the new user
    - Densho Partner
    
    Set up users and passwords: Username for your account
    - ddrclient
    
    Set up users and passwords: Choose a password for the new user
    - ************ [choose a good password, write it down, keep in safe place]
    
    Configure the clock
    - Pacific
    
    Partition disks
    - Guided - use entire disk and set up LVM
    
    Partition disks: Select disk to partition
    - [should only be one option]
    
    Partition disks: Partitioning scheme
    - All files in one partition
    
    Partition disks: Write changes?
    - Yes
    
    Partition disks
    - Finish partitioning and write changes to disk
    
    Partition disks: Write changes?
    - Yes
    
    Configure the package manager: Debian archive mirror country
    - United States
    
    Configure the package manager: Debian archive mirror
    - ftp.us.debian.org
    
    Configure the package manager: HTTP proxy information
    - [leave blank]
    
    Configuring popularity contest: Participate in package usage survey?
    - Yes
    
    Software selection
    - Uncheck everything except "Standard system utilities"
    
    Install GRUB boot loader?
    - Yes

Reboot the VM and log in.



Network interfaces
-------------------------

Edit the networking config file::

    # nano /etc/network/interfaces

so that it looks like the following::

    # This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).
     
    # The loopback network interface
    auto lo
    iface lo inet loopback
     
    # The primary network interface
    allow-hotplug eth0
    iface eth0 inet dhcp
     
    # host-only interface
    auto eth1
    iface eth1 inet static
    address 192.168.56.101
    netmask 255.255.255.0
    network 192.168.56.0
    broadcast 192.168.56.255

Reboot the machine::

    # reboot

Log in and confirm that you have IP addresses for both network interfaces (`eth0` and `eth1`)::

    # ifconfig
    eth0      Link encap:Ethernet  HWaddr 08:00:27:40:b8:f8  
              inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
              inet6 addr: fe80::a00:27ff:fe40:b8f8/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:8988 errors:0 dropped:0 overruns:0 frame:0
              TX packets:4585 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:6956862 (6.6 MiB)  TX bytes:302963 (295.8 KiB)
     
    eth1      Link encap:Ethernet  HWaddr 08:00:27:e8:cc:63  
              inet addr:192.168.56.101  Bcast:192.168.56.255  Mask:255.255.255.0
              inet6 addr: fe80::a00:27ff:fee8:cc63/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:16121 errors:0 dropped:0 overruns:0 frame:0
              TX packets:8454 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:11265980 (10.7 MiB)  TX bytes:3098236 (2.9 MiB)
     
    lo        Link encap:Local Loopback  
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:16436  Metric:1
              RX packets:203 errors:0 dropped:0 overruns:0 frame:0
              TX packets:203 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:41345 (40.3 KiB)  TX bytes:41345 (40.3 KiB)

Ping a common domain name and confirm that you get a response::

    # ping google.com
    PING google.com (74.125.224.168) 56(84) bytes of data.
    64 bytes from lax02s01-in-f8.1e100.net (74.125.224.168): icmp_req=1 ttl=51 time=10.6 ms
    64 bytes from lax02s01-in-f8.1e100.net (74.125.224.168): icmp_req=2 ttl=51 time=9.80 ms
    64 bytes from lax02s01-in-f8.1e100.net (74.125.224.168): icmp_req=3 ttl=51 time=10.6 ms



Security Hardening
-----------------

`ufw` (Uncomplicated Firewall) is a simple interface for the built-in `iptables` software firewall.  The following steps will set the firewall to block all traffic except secure shell (ssh) and HTTP.::

    # apt-get install ufw
    # ufw allow 22/tcp
    # ufw allow 80/tcp
    # ufw allow 9001/tcp
    # ufw enable
    # ufw status
    Status: active
     
    To                         Action      From
    --                         ------      ----
    22/tcp                     ALLOW       Anywhere
    22/tcp                     ALLOW       Anywhere (v6)
    80/tcp                     ALLOW       Anywhere
    80/tcp                     ALLOW       Anywhere (v6)
    9001/tcp                   ALLOW       Anywhere
    9001/tcp                   ALLOW       Anywhere (v6)

Install the SSH server and `fail2ban`, a daemon that shuts down some types of automated SSH hacking::

    # apt-get install openssh-server fail2ban

Disable login for `root`.  Find the line containing `PermitRootLogin` and change the setting from `yes` to `no`.::

    # nano /etc/ssh/sshd_config

Restart SSH::

    # /etc/init.d/ssh restart



Log in from your host machine
-----------------------------

At this point, log out from the VirtualBox window and log in from your host machine using SSH.  It is often more convenient to work from your host OS through an SSH connection.  Exactly how you do this will depend on whether you're using PuTTY or Cygwin, the OS X terminal, or a Linux terminal.  Whatever the case, you should now be able to log in as your regular user.  You should *not* be able to log in as root.



DDR Applications and Dependencies - Automated Installation
==========================================================

In this section we will use a script to automatically install the DDR code and its supporting applications.

Log in to your VM and become `root`.  Add a `ddr` user::

    # adduser ddr
    [enter info]

Then install the prerequisites and install the `ddr-public` app itself.::

    # apt-get install git-core
    # git clone https://github.com/densho/ddr-public.git /usr/local/src/ddr-public
    # cd /usr/local/src/ddr-public/ddrpublic
    # make install

Wait as Make installs Debian packages and Python code and builds up your system.  On a basic VM this takes between 5-10 minutes.

If this instance of `ddr-public` is to be used for development or staging, edit `/etc/ddr/local.cfg` to include the following text.::

    [public]
    docstore_host=127.0.0.1:9200
    docstore_index=stage

To get the nice Django error messages edit `/usr/local/src/ddr-public/ddrpublic/ddrpublic/settings.py` and set `DEBUG = True`.  WARNING: setting `DEBUG = True` in a production environment is a security risk!

Create database tables for installed applications.  `ddr-public` uses the Django ORM to store data about locally-created thumbnail images.::

    # make syncdb

Repository-wide specifications and configurations are stored in a `ddr` repo.  Create a base directory and clone the `ddr` repo to it.::

    # mkdir /var/www/media/base/
    # git clone USER@HOST:PATH/ddr.git /var/www/media/base/ddr/

Restart the servers and the web application.::

    # make restart



Uploading Media
===============

If you want to display media on your site you need to upload it to a server so that `ddrpublic` can see it.
The thumbnail module can only work with media that is directly accessible (e.g. not via a CDN or behind a redirect), so you may need to specify an alternate media URL.::

    [public]
    media_url=
    media_url_local=

    



Populating the Elasticsearch database
=====================================

At this point the `ddr-public` application is installed but Elasticsearch has no data to work with.

The following steps should be run on a machine with `ddr-local` that has local access to the collections you want `ddr-public` to make visible.

Upload repository-wide data::

    $ ddrindex create -h HOST:PORT
    $ ddrindex vocabs -h HOST:PORT /opt/ddr-local/ddr-vocab/api/0.2/
    $ ddrindex narrators -h HOST:PORT /opt/ddr-cmdln/densho-vocab/api/0.2/narrators.json
    $ ddrindex repo -h HOST:PORT /var/www/media/ddr/ddr/repository.json

Upload data for each organization::

    $ ddrindex org -h HOST:PORT /var/www/media/ddr/REPO-ORG/organization.json

Upload data for individual collections::

    $ ddrindex publish -h HOST:PORT --recurse /var/www/media/ddr/REPO-ORG-ID
