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

Normal Debian VM setup (see elsewhere).  Make sure you have `openssh-server`, `ufw`, and `fail2ban` installed.


Gitolite3
--------------------

Install packages::

  # apt-get install git git-annex gitolite3

During the install it will ask you for the administrator's SSH key; leave this blank for now.

Create a git user::

  # adduser --system --shell /bin/bash --gecos 'git version control' \
     --group --disabled-password --home /home/git git

Designate a user as the initial gitolite administrator and generate an SSH keypair for them.::

  $ ssh-keygen -t rsa -b 4096 -C "YOUR_EMAIL@EXAMPLE.COM"

Copy the gitolite administrator user's public SSH key to the `git` user's dir::

  # cp /home/ADMINUSER/.ssh/id_rsa.pub /home/git/ADMINUSER.pub
  # chown git.git /home/git/admin.pub

Run gitolite's setup::

  # su - git
  $ gitolite setup -pk admin.pub
  $ rm admin.pub

This generates a `/home/git/repositories/` directory in which will reside all the repos managed by Gitolite, including the `gitolite-admin` repo used to set access permissions.

TODO Migration process

Edit `/home/git/.gitolite.rc` to enable the git-annex-shell command.  Find the ENABLE list and add this line in there somewhere::

  'git-annex-shell ua',

Reference:

* http://git-annex.branchable.com/tips/using_gitolite_with_git-annex/  Note: We can't follow the steps in order because we installed from packages.


Cgit
----

Install packages::

  # apt-get install nginx fcgiwrap cgit

Edit `/etc/group` and add the Nginx user `www-data` to the `git` group::

  git:x:1002:www-data

Make the `git` user's home dir and `repositories` dir readable by the `git` group::
  
  # chmod 750 /home/git/
  # chmod -R 750 /home/git/repositories/

Edit `/home/git/.gitolite.rc` and set UMASK to `0027` so repository files are visible to Nginx and thus cgit.

Cgit provides access to files in a specified directory.  Nginx must be able to read all directories along a path so we cannot simply point Cgit at `/home/git/repositories/`.  Change this to be a symlink to the gitolite repositories.  ::

  # ln -s /home/git/repositories/ /var/www/repositories/

Edit the Cgit config file `/etc/cgitrc`.  Include at least the following::

  #
  # cgit config
  # see cgitrc(5) for details
   
  css=/cgit.css
  logo=/cgit.png
   
  virtual-root=/
   
  # Where cgit will look for repositories.
  scan-path=/var/www/repositories/
   
  # Text printed as heading on the repository index page.
  root-title=HUB SERVER TITLE HERE
   
  # Text printed below the heading on the repository index page.
  root-desc=HUB SERVER DESCRIPTION HERE

Edit the Nginx config file, adding the following to the relevant `server` block::

  server {
      listen  80 default_server;
      listen  [::]:80 default_server;
      root  /usr/share/cgit;
      try_files  $uri @cgit;
      
      location @cgit {
          include  fastcgi_params;
          fastcgi_param  SCRIPT_FILENAME /usr/lib/cgit/cgit.cgi;
          fastcgi_param  PATH_INFO       $uri;
          fastcgi_param  QUERY_STRING    $args;
          fastcgi_param  HTTP_HOST       $server_name;
          fastcgi_pass  unix:/run/fcgiwrap.socket;
      }
  }

Reference:

* https://levlaz.org/installing-cgit-nginx-on-debian-jessie/
* https://wiki.archlinux.org/index.php/Cgit


  
ddr-workstation
---------------

