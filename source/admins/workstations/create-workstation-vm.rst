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

- [VirtualBox may do the following steps for you automatically.]
- Run VirtualBox.
- In the "VirtualBox Manager" window, click "File > Preferences" and select the "Extensions" tab.
- Click the "Add package" icon on the right side of the window.
- Locate the "VirtualBox $VERSION_NUMBER Oracle VM VirtualBox Extension Pack" that we downloaded earlier, then click "OK".



Host-only Network
-----------------

By default, VMs can only connect to the outside Internet.  VirtualBox's host-only network is used for communications between VMs and with your host OS.

- In the "VirtualBox Manager" window, click "File > Host Network Manager".
- There should be a host-only network already created. Select it and click the Properties tab.
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
    - Version: Debian (64-bit)
    
    Memory Size
    - 2048 MB
    
    Hard Drive
    - Create a virtual hard drive now
    
    Hard drive file type
    - VDI
    
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
- Folder Path: Browse and select folder.
- Folder Name: "ddrshared"
- Read-only: NO
- Auto-mount: YES
- Mount point: /media/sf_ddrshared
- Make Permanent: YES (this option may not appear until VM has been rebooted)


Download Debian Stable
---------------------------------

- http://www.debian.org/distrib/
- Click on "64-bit PC netinst iso" to start the download.
- Note the location of the downloaded file: `$DEBIAN_ISO`.



Install Debian on the VM
------------------------

With a few exceptions, just accept the default option for each step in the Debian installer.::

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
    - enp0s3

    Configure the network: Hostname
    - Enter your VM name
    
    Configure the network: Domain name
    - ddrlocal$NUM
    
    Set up users and passwords: Root password
    - ************ [choose a good password, write it down, keep in safe place]
    
    Set up users and passwords: Full name for the new user
    - Densho Partner
    
    Set up users and passwords: Username for your account
    - [orginization name]
    
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
    
    Configure the package manager: Scan another CD or DVD?
    - No
    
    Configure the package manager: Debian archive mirror country
    - United States
    
    Configure the package manager: Debian archive mirror
    - deb.debian.org

    Configure the package manager: HTTP proxy information
    - [leave blank]
    
    Configuring popularity contest: Participate in package usage survey?
    - Yes
    
    Software selection
    - Uncheck everything except "Standard system utilities"
    
    Install GRUB boot loader?
    - Yes
    
    Device for boot loader installation:
    - Select `ata-VBOX_HARDDISK_*` or similar.

Reboot the VM and log in as `root`.



Security Hardening
-----------------

`ufw` (Uncomplicated Firewall) is a simple interface for the built-in `iptables` software firewall.  The following steps will set the firewall to block all traffic except secure shell (ssh) and HTTP.::

    # apt-get install ufw
    # ufw allow 22/tcp
    # ufw allow 80/tcp
    # ufw allow 443/tcp
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
    443/tcp                    ALLOW       Anywhere
    443/tcp                    ALLOW       Anywhere (v6)
    9001/tcp                   ALLOW       Anywhere
    9001/tcp                   ALLOW       Anywhere (v6)

Install the SSH server and `fail2ban`, a daemon that shuts down some types of automated SSH hacking::

    # apt install openssh-server fail2ban

Disable login for `root`.  Find the line containing `PermitRootLogin` and change the setting from `prohibitpassword` to `no`.::

    # nano /etc/ssh/sshd_config

Restart SSH::

    # service ssh restart



Useful tools
-----------------

These are tools that are highly useful during the install and in day-to-day troubleshooting and monitoring.

    # apt update && apt install sudo byobu htop nfs-common



Install VirtualBox Guest Additions
----------------------------------

source: http://virtualboxes.org/doc/installing-guest-additions-on-debian/

Install VirtualBox Guest Additions, which is required
for accessing shared directories on the host system.
Before running these steps, highlight the VM window and click "Devices > Insert Guest Additions CD Image", then log in as root.
::
    # Update your APT database
    apt update
    # Install the latest security updates
    apt upgrade
    # Install required packages
    apt install build-essential module-assistant
    # Configure your system for building kernel modules
    m-a prepare
    # Mount the CD-ROM
    mount /media/cdrom.
    # Install the guest additions
    sh /media/cdrom/VBoxLinuxAdditions.run

If you get an error while trying to mount the Guest Additions CD see https://askubuntu.com/questions/573596/unable-to-install-guest-additions-cd-image-on-virtual-box/960324#960324.



Snapshot
--------

You now have a basic Debian Linux server setup.  This is a good time to take a snapshot of your VM.

* In the VirtualBox Manager window, right-click on your VM and choose "Close > Save State".
* Click the "Snapshots" button.
* Click the "Take a Snapshot" icon or type Ctl+Shift+S.
* Wait fo the process to complete.
* Right-click on your VM and click "Start" to resume your installation.

If something goes wrong while installing the DDR, or if the developer makes a non-backwards-compatible change to the software, you can always come back to this point and not have to start from the very beginning.



DDR Applications and Dependencies
=================================

It is recommended to install `ddr-local` from a package repository, since your install will receive upgrades automatically as part of the normal system package update process.

**Adding the Repository**

Download the GPG key for the repository and then add the repository listing. Run the following commands as `root`. Replace `REDACTED` with the master key signature.
::
    # export MASTER=REDACTED
    # mkdir /etc/apt/keyrings/
    # curl https://keys.openpgp.org/vks/v1/by-fingerprint/$MASTER > /etc/apt/keyrings/densho.asc

Next add the appopriate entry to `/etc/apt/sources.list.d`.

[Obsolete] For Debian 11 (Bullseye):
::
    $ echo "deb [signed-by=/etc/apt/keyrings/densho.asc] https://packages.densho.org/debian/ bullseye main" | sudo tee /etc/apt/sources.list.d/densho.list

For Debian 12 (Bookworm):
::
    $ echo "deb [signed-by=/etc/apt/keyrings/densho.asc] http://packages.densho.org/debian/ bookworm main" | sudo tee /etc/apt/sources.list.d/densho.list

For Debian 13 (Trixie):
::
    $ echo "deb [signed-by=/etc/apt/keyrings/densho.asc] http://packages.densho.org/debian/ trixie main" | sudo tee /etc/apt/sources.list.d/densho.list

**Installing the Package**

You can now install the DDR Editor with the following commands (substitute 'master' with a branch name if you are installing a branch, e.g. 'develop'):
::
    $ sudo apt update
    $ sudo apt install ddrlocal-master

*Updating the Editor*

Once the package is installed you can get updates as part of the normal system update/upgrade process:
::
    $ sudo apt update && sudo apt upgrade


The DDR user
------------

IMPORTANT: The editor run as the `ddr` user, which is installed as part of the package install.  In the Densho HQ environment, it is *critical* that the `ddr` user has the uid and gid set to `1001`.  In other cases this likely does not matter.

User uid and gid are set in `/etc/passwd`.  If both of these commands return the same output you are good.
::
    $ cat /etc/passwd | grep ddr
    ddr:x:1001:1001::/home/ddr:/bin/bash
    $ cat /etc/passwd | grep 1001
    ddr:x:1001:1001::/home/ddr:/bin/bash

The installer should have set up the user/group properly but just in case you can change it manually; do this as soon after setting up the VM as possible.
::
    $ cd /opt/ddr-local/
    $ sudo make ddr-user


Network interfaces
-------------------------

Use the Makefile to install a networking config file to set the VM to a standard IP address (192.168.56.101).
::
    $ cd /opt/ddr-local/
    $ sudo make network-config
    $ sudo reboot

Log in and confirm that you have IP addresses for both network interfaces (`eth0` and `eth1`)
::
    $ sudo ip address

Either of these commands should return something like the following
::
    eth0      Link encap:Ethernet  HWaddr 08:00:27:40:b8:f8
              inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
              ...
    
    eth1      Link encap:Ethernet  HWaddr 08:00:27:e8:cc:63
              inet addr:192.168.56.101  Bcast:192.168.56.255  Mask:255.255.255.0
              ...
    
    lo        Link encap:Local Loopback
              inet addr:127.0.0.1  Mask:255.0.0.0
              ...

Ping a common domain name and confirm that you get a response::

    $ ping google.com
    PING google.com (142.251.33.78): 48 data bytes
    64 bytes from 142.251.33.78: icmp_seq=0 ttl=120 time=1.211 ms
    64 bytes from 142.251.33.78: icmp_seq=1 ttl=120 time=1.402 ms
    64 bytes from 142.251.33.78: icmp_seq=2 ttl=120 time=0.644 ms
    ...


Configuration
-------------

Repository-wide specifications and configurations are stored in a `ddr` repo that will be installed below.

Most settings are in `/etc/ddr/ddrlocal.cfg`.  Settings in `/etc/ddr/ddrlocal-local.cfg` will override settings in `/etc/ddr/ddrlocal.cfg`, so `ddrlocal-local.cfg` may be used to customize your setup.  These files are shared by `ddr-local` and `ddr-cmdln`.

Settings specific to Django are in `/opt/ddr-local/ddrlocal/ddrlocal/settings.py`.

If this will be a stand-alone workstation or if you are using a Qumulo-style NFS and this machine will be the one to run the background indexing processes, run the following to set up and start the background process.::

    $ cd /opt/ddr-local/
    $ sudo make enable-bkgnd

`ddr-local` doesn't use the Django ORM for much, but you have to create a database anyway::

    $ cd /opt/ddr-local/
    $ sudo make migrate

As a security precaution, Git will refuse to execute commands in repositories owned by other users unless repository directories are listed as safe in a `.gitconfig` file.  Run this command to mark `ddr-local` source code directories as safe::

    $ cd /opt/ddr-local
    $ sudo make git-safe-dir



Elasticsearch
-------------
Search is optionally provided by Elasticsearch which can be run as a local installation (i.e., ES instance on the VM itself), or against a network-accessible ES cluster located off the VM.

If you wish use a local instance, you can install it thusly:
::
    $ cd /opt/ddr-local/
    $ sudo make get-elasticsearch
    $ sudo make install-elasticsearch

The Elasticsearch service is disabled by default.  To enable it at boot
::
    $ sudo systemctl enable elasticsearch.service

The `ddrindex` command uses the Elasticsearch config from the `[public]` section because reasons. You can override that selection in `/etc/ddr/ddrlocal-local.cfg`.
::
    $ sudo vi /etc/ddr/ddrlocal-local.cfg

Add the following to the local config file.  You must add host and index settings to both the `[local]` and `[public]` settings.  `[local]` settings refer to the Elasticsearch index used by this copy of the DDR editor.  `[public]` settings refer to the index used by the public web sites.  Note that the `[local]` section requires a `docstore_enabled` setting before editor search functions will work.:
::
    [local]
    docstore_enabled=True
    docstore_host=127.0.0.1:9200
    docstore_index=ddrlocal
    git_annex_whereis=False
    vocabs_path=http://partner.densho.org/vocab/api/0.2
    
    [public]
    docstore_host=127.0.0.1:9200
    docstore_index=ddrlocal

In a setup where your ES cluster is not hosted on the VM itself, you will need to replace the `docstore_host` with the IP address:port of the host machine (i.e., `docstore_host=10.1.0.57:9200`). You may also need to change the `docstore_index` if you have set up the index with a name other than `ddrlocal`.

Follow `ddrindex` instructions to set up an Elasticsearch index for local searching.
::
    $ cd /opt/ddr-local
    $ sudo su ddr
    ddr$ source /opt/ddr-local/venv/ddrlocal/bin/activate
    ddr$ ddrindex
    ddr$ ddrindex conf
    ddr$ ddrindex status
    ddr$ ddrindex help



Restart
-------

Restart the servers and the web application to see the effects of your edits.::

    $ cd /opt/ddr-local/
    $ sudo make restart


Switching branches
------------------

*Package Install*

The DDR editor is available in two branches: master and develop.
The master branch is more stable and is intended for production use.
The develop branch is for more cutting edge features that may not be quite ready for the master branch.

It is not recommended that you switch branches manually, as updates will probably damage your install.
If you wish to use the develop branch instead of the master branch, remove `ddrlocal-master` and install `ddrlocal-develop`.
::
    $ sudo apt-get remove ddrlocal-master
    $ sudo apt-get install ddrlocal-develop



Test Drive!
-----------

At this point, you should be able to interact with the DDR-Local web application using a web browser on your host computer. (Restarting nginx or a reboot may be necessary)::

    http://192.168.56.101/

If you do not have a Store installed (see below) you will see errors when you try to view the collections list of other pages that require actual data.



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


**Preparing a USB Drive**

To prepare a USB drive for the DDR,:

- format the drive as NTFS,
- create a `ddr/` directory in the drive's root directory.


**Configuring the VM to use the USB Drive**

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

**Preparing a Disk Image**

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


**Configuring the VM to use the Disk Image**


Bypass Cloudflare And Connect Directly To mits3
===============================================

Cloudflare offers various protections from DDoSers and other miscreants.  We would like to take advantage of those protections for `mits.densho.org`, but they get in the way of our use of that site.  The solution(?) is to bypass DNS and point `mits.densho.org` directly at the server's internal IP address.

Add the internal IP address for `mits.densho.org` to `/etc/hosts` on the VM thusly::
    sudo vi /etc/hosts
    # Add this line to the end of the file:
    192.168.0.8     mits.densho.org

Test the configuration::
    $ ping mits.densho.org
    PING mits.densho.org (192.168.0.8) 56(84) bytes of data.
    64 bytes from mits3 (192.168.0.8): icmp_seq=1 ttl=64 time=0.408 ms
    64 bytes from mits3 (192.168.0.8): icmp_seq=2 ttl=64 time=0.336 ms
    64 bytes from mits3 (192.168.0.8): icmp_seq=3 ttl=64 time=0.359 ms
    ...


Remote Debugger (Pagekite)
==========================

Sometimes debugging cannot be performed by a person physically sitting in front of a machine running the Editor VM.  In these cases `Pagekite <http://pagekite.net/>`_ is often easier than using a remote desktop solution.  Pagekite makes it possible to SSH to a VM and view web pages over an SSH tunnel through a relay server.  A Pagekite account must be present and paid up, Pagekite must be installed and configured on the target machine, and the client machine may need some special configuration.

First go to `Pagekite <http://pagekite.net/>`_, create an account, pay for service, and create one or more kites by specifying a subdomain and entering some random text as a secret.

To set up Pagekite on a target machine, first install the `pagekite` app and copy config files to enable SSH and HTTPS connections.  Then edit the Pagekite account info file, enter the `kitename` and `kitesecret` from the Pagekite account page, and delete the line containing `abort_not_configured`.  Finally ensure that the `pagekite` service does not run automatically on restart, and then start it.
::
    sudo apt install pagekite
    sudo cp /etc/pagekite.d/80_sshd.rc.sample /etc/pagekite.d/80_sshd.rc
    sudo cp /etc/pagekite.d/80_httpd.rc.sample /etc/pagekite.d/80_httpd.rc
    sudo nano /etc/pagekite.d/10_account.rc
    sudo systemctl disable pagekite   # it's ok if you see an error after this
    sudo service pagekite start

When debugging is complete, deactivate the service.  Then edit the Pagekite account info file and **remove** the values for `kitename` and `kitesecret`.  This ensures that `pagekite` cannot restart adn give access to the VM.
::
    sudo service pagekite stop
    sudo nano /etc/pagekite.d/10_account.rc

On client machines you may have to tweak your SSH config before you can connect to a pagekite server.  Information can be found on the Pagekite support page (https://pagekite.net/support/) and in the wiki (https://pagekite.net/wiki/).

On Debian and Ubuntu machines I have needed to install `corkscrew <https://github.com/bryanpkc/corkscrew/>`_ (`sudo apt install corkscrew`) and add the following to my `~/.ssh/config`:
::
    Host *.pagekite.me
      CheckHostIP no
      ProxyCommand corkscrew %h 443 %h %p


Repository-wide configuration
=============================

Repository-wide specifications and configurations are stored in a `ddr` repo.  Create a base directory and clone the `ddr` repo to it.::

    # git clone USER@HOST:PATH/ddr.git /media/DISKNAME/ddr/ddr

Note that if you are testing code you may need to switch branches in the `ddr` repo.  Please see the "Switching branches" section.


