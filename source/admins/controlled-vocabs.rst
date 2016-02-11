.. _guide:

=========================
Controlled Vocabularies
=========================

This page documents controlled vocabularies used by the DDR system.

.. contents::


We currently have two sets of terms in use:

- topics
- facility[sic]

For the time being the master copies of these two vocabularies reside in `.json` files in Git repositories.


Digital Repository (ddr-public)
--------------------

- Browse by Topic/Facility (http://ddr.densho.org/browse/)
- Public REST API (http://ddr.densho.org/api/0.1/facet/)

Data resides in Elasticsearch `ddr-public` index, doctype `facet`.
Each set of terms resides in a single Elasticsearch document.
Data is exposed to servers within the colo LAN via the Elasticsearch REST API.

- http://HOST:9200/ddrpublic-production/facet/topics/
- http://HOST:9200/ddrpublic-production/facet/facility/

Vocab data is uploaded to Elasticsearch by `ddr-index facets` (see below).


Encyclopedia (encyc-front)
--------------------

- Article page "From the Densho Digital Repository" sections
- "From the Densho Digital Repository " detail pages

`encyc-front` gets vocab terms from the `ddr-public` REST API.


Workbench Vocabs API
-------------------------------------

The vocab API on http://partner.densho.org/vocab/ is simply a copy of `ddr-vocab` that is installed on the workbench server (`schoolboy`) and exposed via a web server.

- http://partner.densho.org/vocab/api/0.2/topics.json
- http://partner.densho.org/vocab/api/0.2/facility.json

The `ddr-vocab` repository is located in `/var/www/vocab`.

This API is consumed by various components of the DDR ecosystem:

- `ddr-local`: in the Entity edit form
- `encyc-front`: in the DDR-integration functions of the article updater


Editor (ddr-local)
--------------------

Vocabs are used in the Entity edit view, which gets its data from the Workbench Vocabs API.


Admin (ddr-cmdln)
--------------------

Vocabs are used when you run `ddr-index facets`.  Vocab data comes from the `ddr` repo.

**TODO** Use `ddr-vocab` repo instead.


`ddr` / `ddr-vocab` repositories
-------------------------------------

The master copy of each vocabulary lives in two Git repositories.  At the time of this writing, `ddr-vocab` is used for the Workbench Vocabs API, while `ddr` is used by the `ddr-index facets` command.

- `git@mits.densho.org:ddr.git`, in `vocab/`.
- `git@github.com:densho/ddr-vocab`, in `api/0.2/`.

**IMPORTANT** Terms must be updated in **both** places!

See :doc:`workflows` for update procedure.


Configuration
--------------------

This table indicates where configuration variables are defined in the filesystem, where and how they are defined as Python variables, and how they are used.

+-------------+----------------------+---------------------------+----------------------+--------------------+--------------------+
| Project     | Base config file     |                           | Python definition    | Python variable    | Usage              |
+=============+======================+===========================+======================+====================+====================+
| ddr-local   | /etc/ddr/ddr.cfg     | [local] vocab_terms_url   | ddrlocal/settings.py | VOCAB_TERMS_URL    | Entity edit view   |
+-------------+----------------------+---------------------------+----------------------+--------------------+--------------------+
| ddr-cmdln   | /etc/ddr/ddr.cfg     | [cmdln] vocab_facets_path | DDR/config.py        | FACETS_PATH        | ddr-index facets   |
+-------------+----------------------+---------------------------+----------------------+--------------------+--------------------+
| ddr-public  |                      |                           |                      |                    | Not used           |
+-------------+----------------------+---------------------------+----------------------+--------------------+--------------------+
| encyc-front | /etc/encyc/front.cfg | [ddr] topics_src_url      | front/settings.py    | DDR_TOPICS_SRC_URL | Article updates    |
+-------------+----------------------+---------------------------+----------------------+--------------------+--------------------+
| encyc-front | /etc/encyc/front.cfg | [ddr] api_url             | front/settings.py    | DDR_API            | Article->DDR links |
+-------------+----------------------+---------------------------+----------------------+--------------------+--------------------+

Terminology

- Base config file: File in which settings are first defined.
- Python definition: Point at which settings are defined as Python variables.

