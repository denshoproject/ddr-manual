.. _guide:

========================
Maintaining Workstations
========================

This page describes the process of maintaining and updating DDR partner workstations.


Most of the commands you will type in the Linux virtual machine will be typed as `root`.  To become `root`, type the following at a command prompt::

    $ su - root
    [enter root password]

As per convention, variables in this documentation are written in upper case with underscores, starting with a "$" sign.

.. toctree::


Updating a workstation
======================

The partner workstation is designed to be updated in-place without complete reinstallation of the core software components. The ddr-local application contains a script that can assist with the update process. 
To run the script: 

- Log on to the VM terminal as `root` or su `root`.

    $ su - root
    [enter root password]

- Change to the `ddr-local` source direcotry

    $ cd /usr/local/src/ddr-local

- Download the latest version of code from the main repository

    $ git pull
    
- Run the updater shell script

    $ sh ddrlocal/bin/update.sh

The updater will automatically restart server processes; but, depending on the code updates, it may also be necessary to restart the VM. 
