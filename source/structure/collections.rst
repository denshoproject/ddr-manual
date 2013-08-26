.. _structure:

===========
Collections
===========


**TL;DNR:** Description of the collection level of the repository.


File structure
====================

::

    ddr-densho-1
    ├── changelog
    ├── collection.json
    ├── control
    ├── ead.xml
    ├── files
    │   ├── ddr-densho-1-1/
    │   ├── ddr-densho-1-2/
    │   │    ...
    │   └── ddr-densho-1-N/
    ├── .git
    │   ├── annex
    │   ├── ...
    │   ├── config
    │   └── ...
    └── .gitignore



changelog
--------------------

A text file which records changes made to the collection and its files.



collection.json
--------------------

This file contains all the metadata for the collection and for each of its files.
Metadata for each of the collection's entities is located in ``entity.json`` files inside each entity directory.

``collection.json`` is formatted in JSON (JavaScript Object Notation), a text-based open standard designed for human-readable data interchange.



control
--------------------

A text file which contains basic low-level metadata for the collection.

``control`` files have a format similar to that of Microsoft Windows INI files, that is parsed by the Python `ConfigParser <http://docs.python.org/2.7/library/configparser.html>`_ library.



ead.xml
--------------------

This file contains EAD metadata for the collection.
It is generated based on the contents of the ``collection.json`` file.

**Sample EAD.xml**

::

    <ead>
      <eadheader>
        <eadid/>
        <filedesc/>
        <profiledesc/>
      </eadheader>
      <archdesc>
        <did/>
      </archdesc>
      <bioghist/>
      <scopecontent/>
      <relatedmaterial/>
      <separatedmaterial/>
      <controlaccess/>
      <accessrestrict/>
      <userestrict/>
      <dsc type="combined"><head>Inventory</head>
        <c01>
          <did>
           <unittitle>Joe Tanaka video interview</unittitle>
           <container type="box">1</container>
           <container type="folder">1</container>
          </did>
        </c01>
      </dsc>
    </ead>



files/
--------------------

A directory which contains the collection's entity directories.



.git/
--------------------

The ``.git`` directory contains all versions of all the files in a collection repository in a compressed format.  It is used by the Git software which is used to manage DDR repositories.

.. warning::
    Do not modify the contents of this directory unless you have an expert-level understanding of Git!  Doing so will likely cause damage to your repository and its version history!



.gitignore
--------------------

A file that tells Git what files it can ignore.  Ignored files will not be included in collection repositories.
