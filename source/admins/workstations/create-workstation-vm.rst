.. _guide:

=========================
Creating a Workstation VM
=========================

This page documents the process of creating a Linux virtual machine that can be installed on a user's workstation.


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

The `ddr-local` virtual machine will take in media documents from your workstation, process them, and deposit them on a USB hard drive.
VirtualBox VMs can only access designated directories in the host filesystem.
In order for `ddr-local` to be able to ingest documents, you must designate a directory on your workstation into which you will deposit the documents.

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



Install Miscellaneous Useful Tools
----------------------------------

::

    # apt-get install ack-grep bpython byobu bzip2 curl elinks htop logrotate mg multitail p7zip-full wget




Install VirtualBox Guest Additions
----------------------------------

..note:: Note: Debian 7.4 may have included the VirtualBox extensions in the core installer...stay tuned. (2/11/2014). 

source: http://virtualboxes.org/doc/installing-guest-additions-on-debian/

Install required packages in the VM, then configure system for building kernel modules::

    # apt-get install build-essential module-assistant
    # m-a prepare

In the VM window, click on "Devices > Install Guest Additions". ::

    # mount /media/cdrom
    # sh /media/cdrom/VBoxLinuxAdditions.run



Snapshot
--------

You now have a basic Debian Linux server setup.  This is a good time to take a snapshot of your VM.

* In the VirtualBox Manager window, right-click on your VM and choose "Close > Save State".
* Click the "Snapshots" button.
* Click the "Take a Snapshot" icon or type Ctl+Shift+S.
* Wait fo the process to complete.
* Right-click on your VM and click "Start" to resume your installation.

If something goes wrong while installing the DDR, or if the developer makes a non-backwards-compatible change to the software, you can always come back to this point and not have to start from the very beginning.



DDR Applications and Dependencies - Automated Installation
==========================================================

In this section we will use a script to automatically install the DDR code and its supporting applications.

Log in to your VM and become `root`.  Add a `ddr` user::

    # adduser ddr
    [enter info]

Then install the prerequisites and install the `ddr-local` app itself.::

    # apt-get install git-core
    # git clone https://github.com/densho/ddr-local.git /usr/local/src/ddr-local
    # cd /usr/local/src/ddr-local/ddrlocal
    # make install

Wait as Make installs Debian packages and Python code and builds up your system.  On a basic VM this takes between 5-10 minutes.  If everything finishes without errors, restart the servers and the web application.::

    # make restart
    # make reload

If this will be a stand-alone workstation or if you are using a Qumulo-style NFS and this machine will be the one to run the background indexing processes, run the following to set up and start the background process.::

    # make enable-bkgnd
    # make reload

When you are done, skip the next section and proceed to "Site-Specific Steps".


Switching branches
------------------

If you need to work on a different branch of the code you need to make sure that the entire codebase is on the same branch.

For example, switching to the `batch-edit` branch::

    # make branch BRANCH=batch-edit
    # make update
    # make restart

If the branch you're switching to has made changes to the 'ddr' repo, you may need to switch branches there as well.




DDR Applications and Dependencies - Manual Installation
=======================================================

This section details how to install the DDR code and its supporting applications by hand.  It is basically the same as running the `install.sh` script above but hopefully you will come away knowing more about what's going on under the hood.



Create a `ddr` user
-------------------

Create a `ddr` user; the various DDR applications will run as this user.::

    # adduser ddr
    [enter info]

Add the `ddr` user to the `vboxsf` group so it can access shared folder(s) on the host OS::

    # adduser ddr vboxsf



www-server
----------

::

    # apt-get install nginx

Test that the web browser works by visiting the following URL in a web browser on your host computer.  You should see a welcome message from the web server.::

    http://192.168.56.101/
    [Welcome to nginx!]



cache server
------------

::

    # apt-get install redis-server



ddr-cmdln
---------

Install the `ddr-cmdln` app.::

    # apt-get install git-core git-annex libxml2-dev libxslt1-dev pmount udisks python-dev python-pip
    # cd /usr/local/src
    # git clone https://github.com/densho/ddr-cmdln.git
    # cd /usr/local/src/ddr-cmdln/ddr
    # python setup.py install
    # pip install -r /usr/local/src/ddr-cmdln/ddr/requirements/production.txt

Add the `ddr` user to the `plugdev` group so it can mount USB devices::

    # adduser ddr plugdev



ddr-lint
--------

Install the `ddr-lint` app.::

    # apt-get install libxml2 libxml2-dev libxslt1-dev
    # cd /usr/local/src
    # git clone https://github.com/densho/ddr-lint.git
    # cd /usr/local/src/ddr-lint/ddrlint
    # python setup.py install
    # pip install -r /usr/local/src/ddr-cmdln/ddr/requirements/production.txt



ddr-local
---------

Install the `ddr-local` web app.::

    # apt-get install libssl-dev python-dev libxml2 libxml2-dev libxslt1-dev supervisor
    # cd /usr/local/src
    # git clone https://github.com/densho/ddr-local.git
    # cd /usr/local/src/ddr-local/ddrlocal
    # pip install -r /usr/local/src/ddr-local/ddrlocal/requirements/production.txt



Create Directories
------------------

Create and set permissions for various directories used by the applications::

    # mkdir /etc/ddr
    # mkdir /var/log/ddr
    # mkdir /var/lib/ddr
    # mkdir /var/www
    # mkdir /var/www/media
    # mkdir /var/www/media/cache
    # mkdir /var/www/static
    # mkdir /var/www/static/js
    # chown -R ddr /var/log/ddr/
    # chown -R ddr /var/lib/ddr/
    # chown -R ddr /var/www/media



Configuration Files
-------------------

Copy the various configuration files to their proper locations.  The only file you should ever have to edit is `/etc/ddr/ddr.cfg`.::
    
    # cp /usr/local/src/ddr-local/debian/conf/ddr.cfg /etc/ddr/
    # chown root.root /etc/ddr/ddr.cfg
    # chmod 644 /etc/ddr/ddr.cfg
    
    # cp /usr/local/src/ddr-local/debian/conf/settings.py /usr/local/src/ddr-local/ddrlocal/ddrlocal/
    # chown root.root /usr/local/src/ddr-local/ddrlocal/ddrlocal/settings.py
    # chmod 644 /usr/local/src/ddr-local/ddrlocal/ddrlocal/settings.py
    
    # cp /usr/local/src/ddr-local/debian/conf/supervisord.conf /etc/supervisor/
    # cp /usr/local/src/ddr-local/debian/conf/celeryd.conf /etc/supervisor/conf.d/
    # cp /usr/local/src/ddr-local/debian/conf/gunicorn_ddrlocal.conf /etc/supervisor/conf.d/
    # chown root.root /etc/supervisor/supervisord.conf
    # chown root.root /etc/supervisor/conf.d/celeryd.conf
    # chown root.root /etc/supervisor/conf.d/gunicorn_ddrlocal.conf
    # chmod 644 /etc/supervisor/supervisord.conf
    # chmod 644 /etc/supervisor/conf.d/celeryd.conf
    # chmod 644 /etc/supervisor/conf.d/gunicorn_ddrlocal.conf
    # /etc/init.d/supervisor restart
    
    # cp /usr/local/src/ddr-local/debian/conf/ddrlocal.conf /etc/nginx/sites-available
    # ln -s /etc/nginx/sites-available/ddrlocal.conf /etc/nginx/sites-enabled
    # /etc/init.d/nginx restart



Bootstrap, jQuery, Modernizr
----------------------------

`ddr-local` uses Bootstrap, jQuery, and Modernizr for its user interface.  These are installed in a directory visible to `nginx` and writable by `ddr-local`.::

    # cd /var/www/static
    # wget http://getbootstrap.com/2.3.2/assets/bootstrap.zip
    # 7z x bootstrap.zip
    # cd /var/www/static/js
    # wget http://modernizr.com/downloads/modernizr-latest.js
    # wget http://code.jquery.com/jquery-1.10.2.min.js
    # ln -s jquery-1.10.2.min.js jquery.js



Test Drive!
-----------

At this point, you should be able to interact with the DDR-Local web application using a web browser on your host computer. (Restarting nginx or a reboot may be necessary)::

    http://192.168.56.101/



Snapshot
--------

This would be a good time to take another snapshot of your VM.




Site-Specific Steps
===================

Nearly everything we have done up to this point will be the same from one VM to the next.
The following steps will "personalize" this VM as belonging to a particular user/organization. 

.. note::
    These procedures also require access to the DDR gitolite-admin master repo. For security reasons, do not clone the gitolite-admin repo to the VM itself. All operations with the gitolite-admin repo should only be performed on a secure, trusted machine! 



Generate SSH keys
-----------------

We use Gitolite to manage access to the various repositories on the sandbox server.
Gitolite allows or refuses access based on SSH public keys.
Normally users use their own personal keys.
In our case, each DDR VM has its own unique key.

SSH keys include a username and domain name at the end.  Usually this matches the name of the user to which the key belongs.  In our case, the web applications forevery DDR VM will be running as the user `ddr`, but we want the SSH key to be unique to the VM.

Create a second user with a username matching the organization (`$ORGANIZATION`)::

    # adduser $ORGANIZATION
    [enter info]

Become the `$ORGANIZATION` user and generate a passwordless SSH key. (Accept the default names and paths for the keyfiles -- i.e., `$ORGANIZATION/home/.ssh/id_rsa` and `$ORGANIZATION/home/.ssh/id_rsa.pub` Do not choose custom names or gitolite will not function correctly.).::

    # su - $ORGANIZATION
    $ ssh-keygen -t rsa
    [don't enter a passphrase]
    $ exit

As `root`, copy the newly-created private and public keys to the `ddr` user's home directory and make the keys owned by that user.::

    # cp -R /home/$ORGANIZATION/.ssh /home/ddr
    # chown -R ddr.ddr /home/ddr/.ssh

Copy `ddr`'s **public** key to a machine that has a copy of the gitolite-admin repository (using sFTP or `scp`).  The public key is: `/home/ddr/.ssh/id_rsa.pub`.


Add Workstation access to DDR
-----------------------------

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
    ddr@pnr:~$ ssh git@mits.densho.org
    The authenticity of host 'mits.densho.org (216.168.60.179)' can't be established.
    RSA key fingerprint is a1:0b:04:28:61:88:c6:00:59:4c:8f:36:d3:1f:8c:c8.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'mits.densho.org,216.168.60.179' (RSA) to the list of known hosts.
    PTY allocation request failed on channel 0
    hello testing, this is git@mits.densho.org running gitolite3 v3.2-19-gb9bbb78 on git 1.7.2.5
     
     R W C  ddr-testing-[0-9]+
     R W C  ddr-testing-[0-9]+-[0-9]+
     ...
    Connection to mits.densho.org closed.



USB Hard Drive
--------------

The DDR application is designed to store collection repositories on an attached USB hard drive. This portion of the VM prep procedure should only be performed if the USB drive will be sent along with the VM for installation at the partner site. If the partner has an existing USB drive at their location that will be used for the DDR, this step is unnecessary. The USB drive configuration should be performed at the partner location.


Preparing a USB Drive
---------------------

To prepare a USB drive for the DDR,:

- format the drive as NTFS,
- create a `ddr/` directory in the drive's root directory.


Configuring the VM to use the USB Drive
---------------------------------------

This step configures VirtualBox to automatically attach the USB device to this VM whenever it (the VM) is running.

.. note::
    Once you set up a filter, your VM will expect the USB device to remain attached! If you unplug the device and try to use the VM you will see anomalous behavior!

- Attach the USB hard drive that you plan to use to your computer.
- Wait for the device to appear in your computer's list of drives before proceeding.
- In the VM window, click on "Devices > USB Devices" and select the device in the pop-up menu.  If you have your computer's list of drives visible, you should see the USB device disappear from the list.
- In the VM window, click on "Machine > Settings" and select "USB" from the left-hand side menu.
- Click the "Add Filter From Device" icon and select the device from the pop-up menu.

If you need to remove the device, follow the opposite procedure:

- In the VM window, click on "Machine > Settings" and select "USB" from the left-hand side menu.
- Select the device from the "USB Device Filters" box.
- Click the "Remove USB filter" icon.
- In the VM window, click on "Devices > USB Devices" and un-check the device in the pop-up menu.  If you have your computer's list of drives visible, you should see the USB device reappear in the list.

.. note::
    Because each USB drive will have a unique name/signature, you will need to perform this procedure each time you swap in a new drive for a full one being sent back to Densho HQ.


Disk Image
----------

Alternative to keeping collections on a USB hard drive (1.5.3)

Preparing a Disk Image
----------------------

Before you start, note the device names, filesystems, and sizes for device that are attached to the system.  This is to avoid accidentally reformatting the wrong device.  A number of tools provide this information.::

    $ df 
    Filesystem               1K-blocks      Used Available Use% Mounted on
    rootfs                     7627880   3306624   3933776  46% /
    udev                         10240         0     10240   0% /dev
    tmpfs                       415104       316    414788   1% /run
    /dev/mapper/partner-root   7627880   3306624   3933776  46% /
    tmpfs                         5120         0      5120   0% /run/lock
    tmpfs                       830200         0    830200   0% /run/shm
    /dev/sda1                   233191     19354    201396   9% /boot
    none                     971127804 647786216 323341588  67% /media/sf_ddrshared
    /dev/sdc1                488375968 284164688 204211280  59% /media/WD5000BMV-2
    
    $ lsblk -f
    NAME                      FSTYPE LABEL       MOUNTPOINT
    sda                                          
    ├─sda1                                       /boot
    ├─sda2                                       
    └─sda5                                       
      ├─partner-root (dm-0)                      /
      └─partner-swap_1 (dm-1)                    [SWAP]
    sr0                                          
    sdc                                          
    └─sdc1                    ntfs   WD5000BMV-2

In this case, `/dev/sdc1` is a USB drive formatted as a DDR drive.  `/dev/sda` is not listed here but if you installed according to this document's instructions it contains the boot partition, operating system, and other files for the VM.

Create the new virtual disk in VirtualBox.

- Shut down the VM if it is running.
- In VirtualBox Manager, right-click on the VM and choose Settings.
- Under "Storage," notice that the SATA controller is the second item in the storage tree. Underneath should be a VMDK file named after the VM (e.g. "ddrworkbench.vmdk").
- Click on the SATA controller.  You should see the "Add CD/DVD Device" and "Add Hard Disk" icons. Click the hard disk.
- In the pop-up dialog, choose "Create new disk".
- On the hard drive file type screen select "VMDK". Other choices are certainly valid, though VMDK files are also readable by VMware.
- On the storage type screen choose "Fixed size".
- Give the virtual harddrive file a name and configure the size.

Creating the disk image may take a long time.  When it is finished, start the VM.

- Get the device ID from `fdisk`.  It will be a device that previously didn't exist and so should not match the information you collected above.  Since you just created the disk image it most likely is not formatted, so look for a "Disk /dev/DEVICE doesn't contain a valid partition table" message.  Note that `/dev/sda` is almost certainly the drive that contains the VM's operating system, so don't select that.::

    $ sudo fdisk -l
    [sudo] password for USERNAME:
     
    Disk /dev/sdb: 137.4 GB, 137438953472 bytes
    255 heads, 63 sectors/track, 16709 cylinders, total 268435456 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00000000
     
    Disk /dev/sdb doesn't contain a valid partition table
    
    ...

- Create a single partition that fills the disk image.::

    $ sudo su
    root@pnr:/home/gjost# fdisk /dev/sdb
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0x59565fb0.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.
     
    Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)
    
    Command (m for help): n
    Partition type:
       p   primary (0 primary, 0 extended, 4 free)
       e   extended
    Select (default p): p
    Partition number (1-4, default 1): 1
    First sector (2048-268435455, default 2048): [RETURN]
    Using default value 2048
    Last sector, +sectors or +size{K,M,G} (2048-268435455, default 268435455): [RETURN]
    Using default value 268435455
     
    Command (m for help): w
    The partition table has been altered!
     
    Calling ioctl() to re-read partition table.
    Syncing disks.

- Format the partition as `ext4`.::

    # mkfs.ext4 /dev/sdb1 
    mke2fs 1.42.5 (29-Jul-2012)
    Filesystem label=
    OS type: Linux
    Block size=4096 (log=2)
    Fragment size=4096 (log=2)
    Stride=0 blocks, Stripe width=0 blocks
    8388608 inodes, 33554176 blocks
    1677708 blocks (5.00%) reserved for the super user
    First data block=0
    Maximum filesystem blocks=0
    1024 block groups
    32768 blocks per group, 32768 fragments per group
    8192 inodes per group
    Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000, 7962624, 11239424, 20480000, 23887872
     
    Allocating group tables: done                            
    Writing inode tables: done                            
    Creating journal (32768 blocks): done
    Writing superblocks and filesystem accounting information: done

- Make a directory to serve as the mount point.  Replace "DISKNAME" with the disk image name.::

    # mkdir /media/DISKNAME

- Add the new partition to `fstab` so it is mounted automatically on startup.::

    # vi /etc/fstab
    
    # Add the following line to the end of the file:
    /dev/sdb1    /media/DISKNAME    ext4    defaults,noatime    0    2

- Reboot the VM.

Finally, prep for use with ddr-local.  Make a `ddr` folder at the root of the drive that is owned by the `ddr` user.::

    # mkdir /media/DISKNAME/ddr
    # chown -R ddr.ddr /media/DISKNAME/ddr


Configuring the VM to use the Disk Image
----------------------------------------

