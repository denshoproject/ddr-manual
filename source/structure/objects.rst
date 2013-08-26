.. _structure:

=======
Objects
=======


**TL;DNR:** Description of the object level of the repository.

.. note::
    The word "object" is a reserved word in the Python programming language, so the underlying software instead refers to "entities".  You may occasionally see references to "entities" in the software and documentation.




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

A text file which records changes made to the object and its files.
See the collections page for format information.



control
--------------------

A text file which contains basic low-level metadata for the object and its files.
See the collections page for format information.



entity.json
--------------------

This file contains all the metadata for the object and for each of its files.
It is formatted in JSON (JavaScript Object Notation), a text-based open standard designed for human-readable data interchange.



files/
--------------------

A directory which contains the object's master, mezzanine, and access files.



mets.xml
--------------------

This file contains METS metadata for the object and for each of its files.
It is generated based on the contents of the ``entity.json`` file.
