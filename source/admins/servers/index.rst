.. _guide:

=======
Servers
=======

This document describes how to install and configure a DDR server, to which client/partner VMs will connect, and to which they will upload DDR collection repositories. At the time of this writing, this infrastructure consists of a single server instance running the following daemons:

These instructions assume that you’re logged into the console on some form of *NIX machine: Linux, OS X, or possibly Cygwin.

These instructions were created for a server running Debian 6.0 AKA “Squeeze”. Your mileage may vary if you’re using a different version or OS.

.. toctree::

   ddr-workbench
   public
   gitolite
