.. _stack:

=====
Stack
=====

DDR collection repositories and their associated metadata are basically just a bunch of text files that describe a bunch of binary files.  They are managed using Git, a tool commonly used to manage software source code, and git-annex, a plugin for Git that is used to manage binary files.  The DDR app is essentially a user interface to these two programs.

* Git (http://git-scm.com)
* git-annex (http://git-annex.branchable.com)


ddr-cmdln
--------------------

The low-level DDR code is packaged as a library with a command-line interface.


Workstations
====================

ddr-local
--------------------

Users interact with the DDR using a web application written in Django with Bootstrap and jQuery UI candy.


Hub Server
====================


ddr-workbench
--------------------

Gitolite
--------------------

`Gitolite <http://gitolite.com/gitolite/>`_ is a tool for granting clients the ability to create and update Git repositories on a server without having to create shell accounts for them.
It is a key component of the whole DDR infrastructure.

Our usage of Gitolite is pretty simple.  Basically, we have set up a Gitolite server in accordance with with the Gitolite documentation, managed access to it using `gitolite-admin`, and put all of our repositories in the `/home/git/repositories` directory.


cgit
--------------------


Public Server
====================

ddr-public
--------------------
