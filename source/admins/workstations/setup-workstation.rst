.. _guide:

========================
Setting Up a Workstation
========================

This page documents the process of installing a Linux VM on a local partner machine. These instructions presume that a base DDR VM image has already been created per the process contained in: https://github.com/densho/ddr-manual/blob/master/source/admins/workstations/create-workstation-vm.rst.


NOTE: As per convention, variables in this documentation are written in upper case with underscores, starting with a "$" sign.  

.. contents::
.. section-numbering::



Prepare For The Install
=======================


Install VirtualBox
------------------

**IMPORTANT: For virtualization to function, it may be necessary to enable it from the BIOS settings of the physical machine. For Intel-based hardware, this is usually found under the "Security" section of the BIOS as a setting called, "VT-x Extensions."**

If VirtualBox has not yet been installed on the physical machine, download the software from the official web site and install it.

- https://www.virtualbox.org/wiki/Downloads
- Look for "VirtualBox $VERSION_NUMBER for Windows hosts".
- Also download the "VirtualBox $VERSION_NUMBER Oracle VM VirtualBox Extension Pack".
- When all files are downloaded, launch the installer and install on your host machine, then install the extension pack.



Host-only Network
-----------------

By default, VMs can only connect to the outside Internet.  VirtualBox's host-only network is used for communications between VMs and with your host OS.

- In the "VirtualBox Manager" window, click "File > Preferences", then select the "Network" tab.
- There should be a host-only network already created. Select it and click the screwdriver icon.
- The details for the Host-Only Network should be: (If they do not match, please note for later or, if possible, adjust to these settings.) ::

    Adapter
    - IPv4 Address: 192.168.56.1
    - Server Mask: 255.255.255.0
    
    DHCP Server
    - Enable server: YES
    - Server Address: 192.168.56.100
    - Server Mask: 255.255.255.0
    - Lower Address Bound: 192.168.56.101
    - Upper Address Bound: 192.168.56.254




Install Virtual Machine
=======================



Import VM to partner machine
--------------------------

For instructions on creating the base VM, see: https://github.com/densho/ddr-manual/blob/master/source/admins/workstations/create-workstation-vm.rst

To import the pre-configured base DDR workstation VM into VirtualBox:

- Copy the VM appliance file (.ova) to the host machine 
- Check that the "Default Machine Folder" under "Preferences > General" is set to "C:\\ddrworkbench". You may have to choose, "Other" and then create the new folder.
- Launch VirtualBox and click, "File > Import Appliance", then choose "Open Appliance" and navigate to the .ova file
- In the "Appliance Settings" dialog, check the box, "Reinitialize the MAC address of all network cards." Leave all other settings as is. 
- The new VM should now appear in the VirtualBox Manager inventory.
- Click "Start" and test the VM bootup. (You may see an extended message about keyboard/mouse control which you can ignore.) 

If you encounter a fatal error during bootup of the VM, you may need to turn on PAE/NX extensions. With the VM powered down, choose, "Settings > System" then click on the "Processor" tab. Check the box, "Enable PAE/NX" then hit "OK".


Shared Folders
--------------

The `ddr-local` virtual machine will take in media documents from your workstation, process them, and deposit them on a USB hard drive.
VirtualBox VMs can only access designated directories in the host filesystem.
In order for `ddr-local` to be able to ingest binary master and mezzanine files, you must designate a directory on your workstation into which you will deposit the files. 

- In the host system (i.e., Windows), create a folder that will be used for sharing. **It must be called, "C:\\ddrshared".**
- In the VirtualBox Manager right-click on the VM, select Settings, then choose "Shared Folders" in the window.
- Click "Add shared folder" icon on right side.
- Browse and select the folder (C:\ddrshared) you created above.
  - Name: ddrshared
  - Read-only: NO
  - Auto-mount: YES
  - Make permanent: YES



Test Drive!
-----------

At this point, you should be able to interact with the DDR-Local web application using a web browser on your host computer.::

    http://192.168.56.101/

(This web server IP address assumes that the information in the host-only network settings for VirtualBox matched those above. If they did not, the web server IP may be different.)


USB Hard Drive
==============

The DDR application is designed to store collection repositories on an attached USB hard drive. 

NOTE: THIS DRIVE SHOULD BE USED EXCLUSIVELY BY THE DDR APPLICATION!!

Preparing a USB Drive
---------------------

To prepare a USB drive for the DDR,:

First, format the drive as NTFS. In Windows:

- In the Windows file explorer, find the drive icon.
- Right-click, and choose, "Format."
- In the dialog pop-up, make sure the "File system" is set to, "NTFS (Default)".
- Change the "Volume label" to, "ddr-[PartnerCode]-[Number]". For example, "ddr-hmwf-1".
- Choose "Quick Format", then click "Start". Click "OK" in the warning dialog; then "Close" when finished.

Next, create a folder called, "ddr" in the root directory of the drive. This "ddr" directory must exist for the workbench app to function!


Configuring the VM to use the USB Drive
---------------------------------------

This step configures VirtualBox to automatically attach the USB device to this VM whenever it (the VM) is running. Each USB drive has its own name/signature so each time you swap in a new drive -- for example, when you are ready to send a drive full of master binaries back to Densho -- you will need to first remove the old drive using the second procedure below, then add a filter for the new drive using the first procedure. 

**WARNING: VirtualBox has issues with USB3.0 devices. If you are unable to capture the device from the VM window with an error such as, "Device is busy," try another USB port on your physical machine or use a USB2.0 external hard drive instead.

**IMPORTANT: Once you set up a filter, your VM will expect the USB device to remain attached! If you unplug the device and try to use the VM you will see anomalous behavior!**

- Attach the USB hard drive that you prepared using the instructions above.
- Wait for the device to appear in the Windows file explorer's list of drives before proceeding.
- In the VirtualBox Manager window, choose the workbench VM then "Settings > USB Devices" and select the device in the pop-up menu. (NOTE: the volume name you assigned above will *not* be the name of the USB device on the list. Look for the manufacturer name -- e.g., "Western Digital My Passport 0748 [1010]".) If you have your computer's list of drives visible, you should see the USB device disappear from the list.
- In the VM window, click on "Machine > Settings" and select "USB" from the left-hand side menu.
- Click the "Add Filter From Device" icon and select the device from the pop-up menu.

If you need to remove the device, follow the opposite procedure:

- In the VM window, click on "Machine > Settings" and select "USB" from the left-hand side menu.
- Select the device from the "USB Device Filters" box.
- Click the "Remove USB filter" icon.
- In the VM window, click on "Devices > USB Devices" and un-check the device in the pop-up menu.  If you have your computer's list of drives visible, you should see the USB device reappear in the list.

**NOTE: Because each USB drive will have a unique name/signature, you will need to perform this procedure each time you swap in a new drive for a full one being sent back to Densho HQ.**
