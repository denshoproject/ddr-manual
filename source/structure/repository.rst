.. _structure:

==========
Repository
==========

**TL;DNR:** Top-level descriptive metadata for the repository.

The Digital Repository consists of a number of Stores, each containing a "reository repo", zero or more organization repos, and zero or more collection repos.  The repository repo contains top-level metadata that governs much of the behavior of the editor application and the public presence of the Repository.  This repo must be present on each Store, and named `ddr` in order for it to be recognized by the application.



Directory structure
====================

::

    ddr
    ├── .git
    │   └── ...
    ├── .gitignore
    ├── repository.json
    ├── repo_models
    │   ├── __init__.py
    │   ├── collection.py
    │   ├── entity.py
    │   ├── files.py
    └── vocab
        ├── public.json
        ├── rights.json
        ├── status.json
        ├── topics.json
        └── ...



repository.json
---------------

Sample::

    {
        "id": "ddr",
        "repo": "ddr",
        "url": "http://ddr.densho.org",
        "title": "Densho Digital Repository",
        "description": "DESCRIPTION TEXT HERE",
        "organizations": [
            'ddr-densho',
            'ddr-hmwf',
            'ddr-one',
            'ddr-janm',
        ]
    }

**id**, **repo**:  Keywords for the Repository as a whole.  `repo` is the first component of all object IDs in the Repository.

**url**:  External URL for the Repository.

**title**:  Official name of the Repository.  This field will be used in the public UI.

**description**:  Official description of the Repository as a whole.  This field will be used in the public UI.

**organizations**:  List of object IDs for the organizations that exist, or are planned to exist, in the Repository.  These list items function as pointers to organization repos.



vocab
-----

Data files used to define controlled-vocabularies used by fields in the repository objects defined in `repo_models`.

Example::

    {
        "id": "rights",
        "title": "Rights",
        "description": "RIGHTS DESCRIPTION",
        "terms": [
            {
                "id": "cc",
                "title": "DDR Creative Commons",
                "description": "CC DESCRIPTION"
            },
            {
                "id": "pcc",
                "title": "Copyright, with special 3rd-party grant permitted",
                "description": "PCC DESCRIPTION"
            },
            {
                "id": "nocc",
                "title": "Copyright restricted",
                "description": "NOCC DESCRIPTION"
            },
            {
                "id": "pdm",
                "title": "Public domain",
                "description": "PDM DESCRIPTION"
            }
        ]
    }



repo_models
-----------

Files while define the objects in the Repository (Collections, Objects, Files) and their fields and default values.

Model files must be valid Python modules.



.git/
--------------------

The ``.git`` directory contains all versions of all the files in an inventory repository in a compressed format.  It is used by the Git software which is used to manage DDR repositories.

.. warning::
    Do not modify the contents of this directory unless you have an expert-level understanding of Git!  Doing so will likely cause damage to your repository and its version history!



.gitignore
--------------------

A file that tells Git what files it can ignore.  Ignored files will not be included in inventory repositories.
