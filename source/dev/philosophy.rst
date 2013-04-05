Development Philosophy
======================


Values
~~~~~~




We looked at a number of existing systems.
They seemed to fall into several categories:
- bloated database apps with super-complicated data models and way of exporting data.
- Microsoft-only desktop apps
- huge, monolithic enterprise Java applications that claimed to do everything.
- small PHP apps that looked neat and would run anywhere but had lots of limitations.



The (capital-R) Repository should just be a hierarchical directory structure.
Collections for each organization should all go in a directory for that organization.
Entities should basically consist of the media files plus metadata in text files.

It should be *possible* to maintain the repository using just a text editor and filesystem tools, though it might be tedious.

If you don't like the user interface or the tools, it should be possible to replace them.

It should be possible to lift up the hood and fix things if they need to be fixed.

Not monolithic.

Small pieces loosely joined.


Use existing tools, standards, and architecture wherever possible. Don't reinvent the wheel.



UNIX and TCP/IP style
----------

Small pieces, loosely joined.
Pieces communicate with each other using pipes or RESTful HTTP APIs.



Packages
----------

Inspiration came from the Debian Linux distribution's software package archive.

The package archive gathers a wide variety of materials (software, documentation, media, etc) into a single hierarchical structure.
The structure was essentially just a big directory full of files.
Each package had an internal structure governed by the Policy Manual.
Tools were separate from the policy and from the data.
Could swap out development and admin tools without comprimising the package archive.

The package format was very similar to BagIt.
Existing architecture.


Git
----------

- version control is not just for software developers.
- Distributed is good for historical archives.
- Git is in heavy use by lots of smart people who depend on it for their important projects.
- git-annex made it possible to use Git with large digital media files.



Static generators
----------

In the last couple years I've started to see a trend away from dynamicly-generated everything and back to pre-rendering or static generation.  One advantage that these projects sometimes tout is that blog posts or whatever pages can be edited offline and updated to the server using a process that involves Git rather than a database.  Resulting web sites are faster (only static files) and more secure (less of a stack to hack).

- Frozen Flask (http://pythonhosted.org/Frozen-Flask/)
- Hyde (http://ringce.com/hyde)
- Jekyll (http://jekyllrb.com/)
- Nanoc (http://nanoc.ws/)
- Octopress (http://octopress.org/)
- Pelican
- Sphinx (http://sphinx-doc.org/)
- staticgenerator (Django) (https://pypi.python.org/pypi/staticgenerator/1.3)
