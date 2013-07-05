========================
Setting Up a Workstation
========================

This page documents the process of preparing a Linux virtual machine for use on a local partner workstation.




Prepare For The Install
=======================



Install VirtualBox
------------------

Download VirtualBox from the official web site and install it.

- https://www.virtualbox.org/wiki/Downloads
- Look for "VirtualBox $VERSION_NUMBER for Windows hosts".
- Also download the "VirtualBox $VERSION_NUMBER Oracle VM VirtualBox Extension Pack".
- When all files are downloaded, launch the installer and install on your host machine.



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




Set up Virtual Machine
======================



Create a new virtual machine
----------------------------

In the "VirtualBox Manager" window, click "Machine > New", then select the following options from the various screens that follow::

    Name and operating system
    - Name
    - Type: Linux
    - Version: Debian
    
    Memory Size
    - 1024 MB
    
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

The `ddr-local` virtual machine will take in media documents from your workstation, process them, and deposit them on a USB hard drive.
VirtualBox VMs can only access designated directories in the host filesystem.
In order for `ddr-local` to be able to ingest documents, you must designate a directory on your workstation into which you will deposit the documents.

- Right-click on the VM, select Settings, then choose "Shared Folders" in the window.
- Click "Add shared folder" icon on right side.
- Browse and select folder.
- Give it a name: $SHARED_FOLDER_NAME
  
  - Read-only: NO
  - Auto-mount: YES



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
    - ddrlocal$NUM
    
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

Log in and become root::

    $ su - root
    [enter root password]

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
    address 192.168.56.115
    netmask 255.255.255.0
    network 192.168.56.0
    broadcast 192.168.56.255

Reboot the machine::

    # reboot

Log in again and become root::

    $ su - root
    [enter root password]

Confirm that you have IP addresses for both network interfaces (`eth0` and `eth1`)::

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
              inet addr:192.168.56.114  Bcast:192.168.56.255  Mask:255.255.255.0
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



Software Firewall
-----------------

`ufw` (Uncomplicated Firewall) is a simple interface for the built-in `iptables` software firewall.  The following steps will set the firewall to block all traffic except secure shell (ssh) and HTTP.::

    # apt-get install ufw
    # ufw allow 22/tcp
    # ufw allow 80/tcp
    # ufw enable
    # ufw status
    Status: active
     
    To                         Action      From
    --                         ------      ----
    22/tcp                     ALLOW       Anywhere
    22/tcp                     ALLOW       Anywhere (v6)
    80/tcp                     ALLOW       Anywhere
    80/tcp                     ALLOW       Anywhere (v6)



Secure Shell
------------

Install the SSH server::

    # apt-get install openssh-server

Disable login for `root`.  Find the line containing `PermitRootLogin` and change the setting from `yes` to `no`.::

    # nano /etc/ssh/sshd_config

Restart SSH::

    # /etc/init.d/ssh restart




Install VirtualBox Guest Additions
----------------------------------

source: http://virtualboxes.org/doc/installing-guest-additions-on-debian/

Locate the "VirtualBox $VERSION_NUMBER Oracle VM VirtualBox Extension Pack" that we downloaded earlier.

Install required packages, then configure system for building kernel modules::

    # apt-get install build-essential module-assistant
    # m-a prepare

In the VM window, click on "Devices > Install Guest Additions". ::

    # mount /media/cdrom
    # sh /media/cdrom/VBoxLinuxAdditions.run



USB Hard Drive
--------------

The DDR application is designed to store collection repositories on an attached USB hard drive.
This step configures VirtualBox to automatically attach the USB device to this VM whenever it (the VM) is running.

- Attach the USB hard drive that you plan to use.
- Wait for the device to attach itself to your computer.
- In the VM window, click on "Devices > USB Devices" and select the device from the pop-up menu.
- In the VM window, click on "Machine > Settings" and select "USB" from the left-hand side menu.
- Click the "Add Filter From Device" icon and select the device from the pop-up menu.

If you need to remove the device, follow the opposite procedure:

- In the VM window, click on "Machine > Settings" and select "USB" from the left-hand side menu.
- Select the device from the "USB Device Filters" box.
- Click the "Remove USB filter" icon.




Generate SSH keys
-----------------

We use Gitolite to manage access to the various repositories on the sandbox server.
Gitolite allows or refuses access based on SSH public keys.
Normally users use their own personal keys.
In our case, each DDR VM has its own unique key.

Create a `ddr` user; the various DDR applications will run as this user.
Create a second user with a username matching the organization (`$ORGANIZATION`)::

    $ sudo - root
    # adduser ddr
    [enter info]
    # adduser $ORGANIZATION
    [enter info]

Become the `$ORGANIZATION` user and generate a passwordless SSH key.::

    # su - $ORGANIZATION
    $ ssh-keygen -t rsa
    [don't enter a passphrase]
    $ exit

As `root`, copy the newly-created private and public keys to the `ddr` user's home directory and make the keys owned by that user.::

    # cp -R /home/$ORGANIZATION/.ssh /home/ddr
    # chown -R ddr.ddr /home/ddr/.ssh

Copy `ddr`'s **public** key to a machine that has a copy of the gitolite-admin repository (using sFTP or `scp`).  The public key is: `/home/ddr/.ssh/id_rsa.pub`.

On a machine that has a copy of the `gitolite-admin` repository, add the public key.  Rename the key from `id_rsa.pub` to `$ORGANIZATION@ddrlocal$NUM.pub`::

    $ cd /PATH/TO/gitolite-admin
    $ cp /PATH/TO/id_rsa.pub ./keydir/$ORGANIZATION@ddrlocal$NUM.pub

Edit `conf/gitolite.conf` to grant access to the user.
The organization's section should look like the following.
Please refer to the Gitolite manual for questions).
Replace "organization" with the keyword for the organization, seen elsewhere as `$ORGANIZATION`.::

    # ORGANIZATION - - - - - - - - - - - - -
     
    @organization = organization
     
    repo ddr-organization-[0-9]+
      C     = @admins @densho @organization
      RW+   = @admins
      RW    = @organization
     
    repo ddr-organization-[0-9]+-[0-9]+
      C     = @admins @densho @organization
      RW+   = @admins
      RW    = @organization

Add the pubkey and updated conf file and push to the Gitolite server.::

    $ git add keydir/$ORGANIZATION@ddrlocal$NUM.pub
    $ git add conf/gitolite.conf
    $ git commit -m "Added key: $ORGANIZATION@ddrlocal$NUM.pub"
    $ git push

On the VM, log in as the `ddr` user and confirm that the user now has access.::

    $ su - ddr
    ddr@pnr:~$ ssh git@mits
    The authenticity of host 'mits (192.168.0.14)' can't be established.
    RSA key fingerprint is a1:0b:04:28:61:88:c6:00:59:4c:8f:36:d3:1f:8c:c8.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'mits,192.168.0.14' (RSA) to the list of known hosts.
    PTY allocation request failed on channel 0
    hello testing, this is git@mits running gitolite3 v3.2-19-gb9bbb78 on git 1.7.2.5
     
     R W C  ddr-testing-[0-9]+
     R W C  ddr-testing-[0-9]+-[0-9]+
     ...
    Connection to mits closed.



Install Dependencies
--------------------

::

    # apt-get install fail2ban logrotate
    
    # apt-get install git-core git-annex





ddr-cmdln
=========

Install the `ddr-cmdln` app.::

    # apt-get install libxml2-dev pmount udisks
    # cd /usr/local/src
    # git clone https://github.com/densho/ddr-cmdln.git
    # cd /usr/local/src/ddr-cmdln/ddr
    # python setup.py install
    # pip install -r /usr/local/src/ddr-cmdln/ddr/requirements/production.txt





ddr-local
=========

Install the `ddr-local` web app.::

    # apt-get install libssl-dev python-dev libxml2 libxml2-dev libxslt-dev redis-server supervisor
    # cd /usr/local/src
    # git clone https://github.com/densho/ddr-local.git
    # cd /usr/local/src/ddr-local/ddrlocal
    # pip install -r /usr/local/src/ddr-local/ddrlocal/requirements/production.txt





Configuration
=============

::

    mkdir /etc/ddr
    mkdir /var/lib/ddr
    mkdir /var/log/ddr
    mkdir /var/www/ddr
    mkdir /var/www/ddrlocal
    /etc/ddr/ddr.cfg
    /etc/supervisor/conf.d/gunicorn_ddrlocal.conf
    /etc/nginx/sites-available/ddrlocal.conf
