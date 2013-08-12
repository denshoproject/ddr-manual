.. _structure:

=====
Files
=====


**TL;DNR:** Description of the file level of the repository.



Master files
====================

Master files are the original digital images, exactly as they came out of the scanner.
They will usually contain a ruler for measurement purposes and a color chip for calibration purposes.
As such they are not intended for display.

Master files *are* included in collection repositories.  Due to their large size they *are not* automatically pushed to the central server.

Master images may not be public, or may contain information that is not public.

.. warning::
    TODO: Is the master role encoded only in the name, or in both name and metadata?



Mezzanine files
====================

Mezzanine files are master files that had been resized, cropped, trimmed, color-corrected, or otherwise altered from their original state.

Mezzanine files *are* included in collection repositories.  Due to their large size they *are not* automatically pushed to the central server.

.. warning::
    TODO: Is the master role encoded only in the name, or in both name and metadata?



Access files
====================

Access files are smaller, lower-quality images which are automatically generated from both master and mezzanine files.
They are used not for archival purposes but for display.
In the case of the DDR application, access files are used to generate thumbnails.

Access files *are* included in collection repositories.  Because they are much smaller than masters or mezzanines they *are* automatically pushed to the central server, so that 

Caitlin: I don't think we'll need access (you mean derivatives right?) Versions of masters though.



Thumbnails
====================

The `ddr-local` software automatically generates thumbnails for its user interface.  These images are local: they are associated with that instance of `ddr-local` on that particular computer, and *are not* part of DDR collection repositories.



Relationship between Masters, Mezzanines, and Access files
====================

- A master file may exist without mezzanines.
- A mezzanine may not exist without a master.
- A master may have more than one mezzanine.
- A mezzanine may have multiple masters.  Example: A mezzanine PDF that is composed of multiple master images.

Caitlin: There are some cases like with the Nippu Jiji where the master and mezz will essentially be the same file but there will still be two files, one with the master designation in the file name and one with the mezz designation in the file name.

GFRoh: the relationship between master and mezz is not necessarily 1-to-1. For example, a multi-page doc may be represented by a single PDF mezz that is comprised of many JPG masters. Caitlin, I'm trying to remember if there are cases where there might not be a mezz file for a given master (e.g., a master tif of an envelope with no mezz file...?)



Naming convention
====================
::

    {REPOSITORY}-{ORGANIZATION}-{COLLECTION}-{ENTITY}-{ROLE}-{HASH}[-{APPEND}].{EXTENSION}

REPOSITORY
    Keyword for the repository in which this file resides.  Ex: `ddr`.

ORGANIZATION
    Keyword for the organization to which the file belongs.  Ex: `densho`.

COLLECTION
    ID number of the collection to which the file belongs.  Ex: `42`.

ENTITY
    ID number of the entity with which the file is associated.  Ex: `56`.

ROLE
    Keyword for the role.  Ex: `master` or `mezzanine`.

HASH
    SHA1 hash of the file at the time it was ingested.  Note that because master and mezzanine files are usually different (i.e. mezzanines have been cropped, etc) the SHA1 hashes for master and mezzanine of, for example, a photograph will be different.  Ex: `a9b8c7d6e5`.

APPEND
    Access files will be appended with a keyword to mark them as access files.  Master The append is separate from the role.

EXTENSION
    The file extension, which usually indicates the file type, format, etc.  Ex: `tif`, `jpg`, `pdf`, etc.

Examples
--------------------

Master::

    ddr-densho-42-56-master-a9b8c7d6e5.tif
    ddr-hmfw-303-606-master-a9b8c7d6e5.jpf

Mezzanine::

    ddr-janm-808-909-mezzanine-a9b8c7d6e5.tif
    ddr-one-123-456-master-a9b8c7d6e5.tif

Access::

    ddr-densho-42-56-master-a9b8c7d6e5-a.tif
    ddr-hmfw-303-606-master-a9b8c7d6e5-a.jpf
    ddr-janm-808-909-mezzanine-a9b8c7d6e5-a.tif
    ddr-one-123-456-mezzanine-a9b8c7d6e5-a.tif

