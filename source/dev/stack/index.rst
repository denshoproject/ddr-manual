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

Python dependencies:

* Python (http://python.org)
* BeautifulSoup4 (http://www.crummy.com/software/BeautifulSoup/)
* Envoy (https://github.com/kennethreitz/envoy)
* GitPython (https://pypi.python.org/pypi/GitPython/0.3.2.RC1)
* lxml (http://lxml.de)
* Python XMP Toolkit (http://code.google.com/p/python-xmp-toolkit/)
* pytz (http://pythonhosted.org/pytz/)
* requests (http://docs.python-requests.org/en/latest/index.html)

Non-Python dependencies:

* pmount (https://alioth.debian.org/projects/pmount)
* udisks (https://launchpad.net/udisks)


ddr-local
--------------------

Users interact with the DDR using a web application written in Django with Bootstrap and jQuery UI candy.

* Python (http://python.org)
* Django (http://djangoproject.com)
* jQuery (http://jquery.org)
* Bootstrap (http://getbootstrap.com/2.3.2/)

The following Python libraries are used in the code:

* BeautifulSoup4 (http://www.crummy.com/software/BeautifulSoup/)
* Celery (http://celeryproject.org)
* Envoy (https://github.com/kennethreitz/envoy)
* lxml (http://lxml.de)
* pytz (http://pythonhosted.org/pytz/)
* requests (http://docs.python-requests.org/en/latest/index.html)
* sorl-thumbnail (https://github.com/sorl/sorl-thumbnail)

Non-Python dependencies:

* Graphviz (http://graphviz.org/)
* Imagemagick (http://imagemagick.org)

Daemons:

* Gunicorn (http://gunicorn.org)
* Supervisor (http://supervisord.org/)
* Redis (http://redis.io)
* ElasticSearch (http://www.elasticsearch.org/)
* Lucene (https://lucene.apache.org/)
* Nginx (http://nginx.org)

The DDR workbench app runs in a Linux virtual machine running in VirtualBox.

* Linux (http://kernel.org)
* VirtualBox (http://virtualbox.org)
