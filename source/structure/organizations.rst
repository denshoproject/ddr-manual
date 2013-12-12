.. _structure:

=============
Organizations
=============


**TL;DNR:** Description of the organization level of the repository.


Inventory
--------------------

At an abstract level, DDR consists of a set of collection repos which reside on a number of computers.  Repos may reside on a USB hard drive, in a directory on a hard drive attached to a workstation or server, or possibly in some other type of storage.  This project uses the word **store** to denote a location where collection repositories may reside.

Each organization has an **inventory** -- a set of metadata used to track which stores contain instances of which collection repositories.  Store records also contain basic metadata about the store media, such as physical location and purchase date.

The inventory is a Git repository just like the collection repos.  Each store contains a clone of the relevant organization's inventory; the clone is updated and changes are synced when the collection repos on the store are modified.

The metadata in the inventory makes it possible to track the locations and status of collections, objects, and individual files across the DDR system.  It is also used for operations that affect multiple repos, such as cloning of syncing the contents of one store with another.


Directory structure
--------------------

::

    ddr-densho
    ├── .git
    │   └── ...
    ├── .gitignore
    ├── organization.json
    ├── STORE-1.json
    ├── STORE-2.json
    ├── STORE-3.json
    └── ...


organization.json
--------------------

This file contains basic metadata for the organization.

``organization.json`` is formatted in JSON (JavaScript Object Notation), a text-based open standard designed for human-readable data interchange.

File format::

    {
        "id": "ddr-testing",
        "org": "testing",
        "repo": "ddr"
    }


STORE-N.json
--------------------

These files contain information about the store and about the collection repos it contains.

``STORE-N.json`` files are formatted in JSON (JavaScript Object Notation), a text-based open standard designed for human-readable data interchange.

File format::

    {
      "collections": [
        {
          "cid": "ddr-testing-11",
          "level": "all",
          "uuid": "26813729-b1b1-1e31-ff48-f1cfa6efa5e1",
          "entities": [
            { "eid": "ddr-testing-11-01", "level": "access" },
            { "eid": "ddr-testing-11-11", "level": "access" },
            { "eid": "ddr-testing-11-23", "level": "access" },
            ...
          ]
        },
        {
          "cid": "ddr-testing-12",
          "level": "all",
          "uuid": "92681372-1b1b-11e3-8ff4-1f1cfa6efa5e",
          "entities": [
            { "eid": "ddr-testing-12-34", "level": "meta" },
            { "eid": "ddr-testing-12-48", "level": "meta" },
            { "eid": "ddr-testing-12-35", "level": "meta" },
            ...
          ]
        },
        ...
       ],
       "label": "TESTING201303",
       "location": "Pasadena",
       "org": "testing",
       "purchase_date": "2013-03-31",
       "repo": "ddr"
    }



.git/
--------------------

The ``.git`` directory contains all versions of all the files in an inventory repository in a compressed format.  It is used by the Git software which is used to manage DDR repositories.

.. warning::
    Do not modify the contents of this directory unless you have an expert-level understanding of Git!  Doing so will likely cause damage to your repository and its version history!



.gitignore
--------------------

A file that tells Git what files it can ignore.  Ignored files will not be included in inventory repositories.
