Maintaining Workstations

This page describes the process of maintaining and updating DDR partner workstations.

Updating a workstation

The partner workstation is designed to be updated in-place without complete reinstallation of the core software components. The ddr-local application contains a script that can assist with the update process. 
To run the script: 

- Log on to the VM terminal as root.
- cd /usr/local/src/ddr-local
- git pull
- sh ddrlocal/bin/update.sh

The updater will automatically restart server processes; but, depending on the code updates, it may also be necessary to restart the VM. 
