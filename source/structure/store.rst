.. _structure:

==========
Store
==========

**TL;DNR:** Top-level descriptive metadata for the repository.


The Repository exists as one or more Stores.

Each Store is a directory in a filesystem which contains
- a **repository repo**,
- zero or more **organization repos**, and
- zero or more **collection repos**.

Stores may be located on fileservers, on hard drives, on virtual hard drives within VirtualBox, or on external USB hard drives.



Directory structure
====================

::

    ddr
    ├── ddr
    ├── ddr-densho
    ├── ddr-densho-1
    ├── ddr-densho-2
    ├── ddr-densho-3
    ├── ...
    ├── ddr-hmwf
    ├── ddr-hmwf-1
    ├── ddr-hmwf-2
    ├── ddr-hmwf-3
    └── ...
    └── tmp

