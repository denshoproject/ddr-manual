========
Gitolite
========

`Gitolite <http://gitolite.com/gitolite/>`_ is a tool for granting clients the ability to create and update Git repositories on a server without having to create shell accounts for them.
It is a key component of the whole DDR infrastructure.
Exactly how Gitolite works is beyond the scope of this manual but it's important to at least be aware of.
In the words of the Gitolite developer,

    | "If you're installing gitolite, you're a "system admin", like it or not."
    | --sitaramc

The Gitolite documentation has a very idiosyncratic, 1990s, free-associative hyperlinking feel to it.  Enjoy!

.. toctree::



Creating a `gitolite-admin` repository
======================================

Gitolite configuration files are kept in a Git repo.  Changing these configurations is a process of pulling, editing, and pushing to the Gitolite server.  See the  the `gitolite administration page`_ for more info.

.. _`gitolite administration page`: http://gitolite.com/gitolite/admin.html


Adding a user to `gitolite-admin`
=================================

In order to use Gitolite as a normal or admin user, each user must

- generate an SSH keypair,
- add the **public** key to a `gitolite-admin` repository, and
- add the username to the `gitolite.conf` file.

Instructions for adding a pubkey can be found on Gitolite's `adding and removing users page`_.  Other topics are covered on pages linked to from the `gitolite administration page`_.

.. _`adding and removing users page`: http://gitolite.com/gitolite/users.html
.. _`gitolite administration page`: http://gitolite.com/gitolite/admin.html

SSH pubkey filess are named `id_rsa.pub`.  Before you add a key to `gitolite-admin`, rename it in the form `USERNAME@HOSTNAME.pub`.  This will enable a single *username* to access Gitolite from multiple hosts.  Look inside the key, note the username and hostname.::

    ssh-rsa A1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3c
    A1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3
    cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC
    3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2b
    C3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1aB2bC3cA1a gjost@memex

In this example the username is `gjost` and the hostname is `memex`; the pubkey file would be renamed `gjost@memex.pub`.

The same person might want to access Gitolite from a different machine.  In this case the key might look like this::

    ssh-rsa d4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEF
    d4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DE
    Fd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6D
    EFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6
    DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e5f6DEFd4e gjost@sangabriel

This pubkey would be named `gjost@sangabriel`.


Administration permisions for the `gitolite-admin` repository itself are granted via the `gitolite.conf` file contained within the repo.  Admin users must have `RW+` access.  In our case, this means being added to the `@admins` group.


Clone a copy of `gitolite-admin`
--------------------------------

Once your SSH pubkey has been added to `gitolite-admin` and you have been made a member of the `@admins` group, you can clone the repo using one of the following commands or a variation.  Exactly what you use depends on whether you are inside or outside a firewall, the contents of your local machine's `/etc/hosts` directory, etc.::

    git clone git@mits:gitolite-admin.git
    git clone git@192.168.0.14:gitolite-admin.git
    git clone git@partner.densho.org:gitolite-admin.git

.. note::
    Make sure you use the user `git` and not your own username.  All Gitolite-managed traffic is handled by the Gitolite user, which in this case is `git`.
