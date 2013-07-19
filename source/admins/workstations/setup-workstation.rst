========================
Setting Up a Workstation
========================

This page documents the process of installing a Linux VM on a local partner machine. These instructions presume that a base DDR VM image has already been created per the process contained in: https://github.com/densho/ddr-manual/blob/master/source/admins/create-workstation-vm.rst.


NOTE: Most of the commands you will type in the Linux virtual machine will be typed as `root`.  To become `root`, type the following at a command prompt::

    $ su - root
    [enter root password]

As per convention, variables in this documentation are written in upper case with underscores, starting with a "$" sign.  

.. toctree::



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
- The details for the Host-Only Network should be (If they do not match, please note for later or, if possible, adjust to these settings.) ::

    Adapter
    - IPv4 Address: 192.168.56.1
    
    DHCP Server
    - Enable server: YES
    - Server Address: 192.168.56.100
    - Server Mask: 255.255.255.0
    - Lower Address Bound: 192.168.56.101
    - Upper Address Bound: 192.168.56.254




Install Virtual Machine
=======================



Copy VM to partner machine
--------------------------

For instructions on creating the base VM, see: https://github.com/densho/ddr-manual/blob/master/source/admins/create-workstation-vm.rst

Networking
----------

Confirm that client networking works



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



Network interfaces
-------------------------



Test Drive!
-----------

At this point, you should be able to interact with the DDR-Local web application using a web browser on your host computer.::

    http://192.168.56.101/

(This web server IP address assumes that the information in the host-only network settings for VirtualBox matched those above. If they did not, the web server IP may be different.)


USB Hard Drive
==============

The DDR application is designed to store collection repositories on an attached USB hard drive.
This step configures VirtualBox to automatically attach the USB device to this VM whenever it (the VM) is running.

.. important::
    Once you set up a filter, your VM will expect the USB device to remain attached!
    If you unplug the device and try to use the VM you will see anomalous behavior!

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



Preparing a USB Drive
---------------------

To prepare a USB drive for the DDR, ensure that:

- the drive is formatted as NTFS,
- the drive's root directory contains a `ddr/` directory.
