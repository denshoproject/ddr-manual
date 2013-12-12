.. _stack:

=====
Stack
=====

Git and git-annex are the primary tools used to manage collection repositories.
git-annex is a plugin for Git that is used to manage binary files.
It is the component that makes this architecture feasible at all.
The DDR app is essentially a user interface to these two programs.

* Git (http://git-scm.com)
* git-annex (http://git-annex.branchable.com)

Users interact with the DDR through their web browsers.
The server app runs in a Linux virtual machine running in VirtualBox.

* Linux (http://kernel.org)
* VirtualBox (http://virtualbox.org)

The DDR app is written almost entirely in Python.
The code that actually does the work is packages as a library with a command-line interface.
The part that users interact with is a web application written in Django, with a little jQuery UI candy.

* Python (http://python.org)
* Django (http://djangoproject.com)
* jQuery (http://jquery.org)
* Bootstrap (http://getbootstrap.com/2.3.2/)

The following Python libraries are used in the code:

* Celery (http://celeryproject.org)
* GitPython (https://pypi.python.org/pypi/GitPython/0.3.2.RC1)
* lxml (http://lxml.de)
* Python XMP Toolkit (http://code.google.com/p/python-xmp-toolkit/)
* requests (http://docs.python-requests.org/en/latest/index.html)
* sorl-thumbnail (https://github.com/sorl/sorl-thumbnail)

Servers and dependencies:

* Gunicorn (http://gunicorn.org)
* Imagemagick (http://imagemagick.org)
* Nginx (http://nginx.org)
* Redis (http://redis.io)
