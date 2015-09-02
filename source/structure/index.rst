.. _guide:

============
Repository Structure
============

.. toctree::
   :maxdepth: 1

   dvcs
   repository
   organizations
   collections
   objects
   files
   store
   workstations
   servers


This is a high-level description of the structure of the Densho Digital Repository.


Git and git-annex
--------------------

Git and git-annex are the underlying technologies that make the repository as a whole work.

**repository AKA repo**

In Git the word `repository` is used to denote a directory of files that is managed as a unit by Git.  A Git repository maintains the history of all files that it contains.  In hopes of reducing confusion, we will refer to a Git repository (i.e. a directory of files managed as a unit by Git) as a `repo`.


Repository
--------------------

A Git repo representing the Repository as a whole.  Contains metadata files that define the following:

* keyword
* logo
* title
* description
* Definitions of collection and object data structures
* Controlled Vocabulary to be used 


Organizations
--------------------

Each organization has its own Git repo that defines the following:

* keyword
* logo
* title
* description

The organization repo is also used to track the identities and locations of the various Stores on which Collections physically reside.


Collections
--------------------

A Git repo containing a single archival collection, managed as a unit.
Collections may contain one or more Objects.


Objects
--------------------


Files
--------------------


Stores
--------------------

A Store is a directory that contain a Repository repo, one or more Organization repos, and one or more Collections.


Workstations
--------------------


Servers
--------------------
