.. _structure:

========
Entities
========


**TL;DNR:** Description of the intellectual entity level of the repository.



File structure
====================

::

    ddr-densho-1-1
    ├── changelog
    ├── control
    ├── entity.json
    ├── files
    │   └── ddr-densho-1-1-master-a9b8c7d6e5.jpg
    │   └── ddr-densho-1-1-master-a9b8c7d6e5-a.jpg
    │   └── ddr-densho-1-1-mezzanine-9a8b7c6d5e.jpg
    │   └── ddr-densho-1-1-mezzanine-9a8b7c6d5e-a.jpg
    └── mets.xml



changelog
--------------------

A text file which records changes made to the entity and its files.
See the collections page for format information.



control
--------------------

A text file which contains basic low-level metadata for the entity and its files.
See the collections page for format information.



entity.json
--------------------

This file contains all the metadata for the entity and for each of its files.
It is formatted in JSON (JavaScript Object Notation), a text-based open standard designed for human-readable data interchange.



files/
--------------------

A directory which contains the entity's master, mezzanine, and access files.



mets.xml
--------------------

This file contains METS metadata for the entity and for each of its files.
It is generated based on the contents of the ``entity.json`` file.
