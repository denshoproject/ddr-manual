.. _guide:

==========
Hub Server
==========

This page documents the process of creating a Linux virtual machine to serve DDR repositories to the public.


Most of the commands you will type in the Linux virtual machine will be typed as `root`.  To become `root`, type the following at a command prompt::

    $ su - root
    [enter root password]

As per convention, variables in this documentation are written in upper case with underscores, starting with a "$" sign.

.. contents::


Applications
============

The following applications run on the Hub server:

* Gitolite
* Cgit
* DDR Workbench


Prepare For The Install
=======================
