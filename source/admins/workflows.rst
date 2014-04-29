.. _guide:

=========================
DDR Workflows
=========================

This page documents the data workflows in the DDR system.

.. contents::
.. section-numbering::


Repo Workflow
=======================

Collection Repos
-------------------------------------------

The basic data unit of the DDR system is the Collection, which is instantiated as a git repo. The git repo holds a structured directory of metadata text files as well as the git annex repo info and -- in some cases -- the annex file binary data itself. 

DDR Collection repos are always named using the DDR ID convention::

    ddr-[PartnerID]-[CollectionIDPart]
    E.g., "ddr-densho-2"

Importing Entities and Files
-------------------------------------------

The standard method for working with the DDR is through the ddr-local web ui; however, it is also possible to create new Entities and Files using the manual batch import scripts. 

The commands are available with `ddr-cmdln` and `ddr-local` installed. Both should be on the `master` branch.

To use the Entities importer:

1. Make certain the target Collection repo is located where the VM can access it. The Collection repo must already exist!
2. Prep a valid Entities CSV file and place in a directory that the VM can access. A valid import file must be well-formed CSV that contains the following headers::

   id,status,public,title,description,creation,location,creators,language,genre,format,extent,contributors,alternate_id,digitize_person,digitize_organization,credit,topics,persons,facilities,parent,rights,rights_statement,notes

3. Log into a command-line session as the `ddr` user and start an interactive python session.::

   su ddr
   cd /usr/local/src/ddr-local/ddrlocal
   ./manage.py shell -i bpython
   
4. In the python shell, run the importer method.::

    from importers import densho
    user='Your Name'
    mail='your.email@densho.org'
    collection='/PATH/TO/ddr-repo-name
    csv='/PATH/TO/ddr-repo-name-entities-data.csv'
    densho.import_entities(csv,collection,user,mail)
    
5. The importer will send status messages for each entity create operation to the screen; you can capture the terminal output and log if necessary.

To use the Files importer:

1. Prep valid CSV file and place in a directory with the import binaries that the VM can access.

1. Make certain the target Collection repo is located where the VM can access it. The Collection repo and any Entity to which you would like to attach Files must already exist!
2. Prep a valid Files CSV file and place in a directory that the VM can access. A valid import file must be well-formed CSV that contains the following headers::

   entity_id,file,role,public,rights,digitize_person,tech_notes,label,sort
   
3. Log into a command-line session as the `ddr` user and start an interactive python session.::

   su ddr
   cd /usr/local/src/ddr-local/ddrlocal
   ./manage.py shell -i bpython
   
4. In the python shell, run the importer method.::

    from importers import densho
    user='Your Name'
    mail='your.email@densho.org'
    collection='/PATH/TO/ddr-repo-name
    csv='/PATH/TO/ddr-repo-name-files-data.csv'
    densho.import_files(csv,collection,user,mail)
    
5. The importer will send status messages for each entity create operation to the screen; you can capture the terminal output and log if necessary.


Publishing Repos
-------------------------------------------

The following details the procedure for publishing completed Collection repos. This is specific to the archival processes and operational environment of the DDR project at Densho. 

The commands are available with `ddr-cmdln` and `ddr-local` installed. `ddr-cmdln` should be on the `137-browse-ui` branch.

At Densho HQ, using "ddr-testing-1" example collection repo:

1. Move/copy ddr-testing-1 from import staging to /densho/kinkura/gold/ddr-testing-1::

    mv /densho/drstores/ddr1/ddr-testing-1 /densho/kinkura/gold/ddr-testing-1

2. Review and approve using ddr-local webui.
3. Run ddrfilter, pointing output to /densho/kinkura/working::

    su ddr
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrfilter -ma -s /densho/kinkura/gold/ddr-testing-1 -d /densho/kinkura/working

   Result::
    
    ddr@kinkura:/densho/kinkura/working# ls
    FILTER_ddr-densho-testing-1
    FILTER_ddr-densho-testing-1.log
    FILTER_ddr-densho-testing-1.sh
    
4. Run the generated filtering script::

    sh /densho/kinkura/working/FILTER_ddr-densho-testing-1.sh | tee -a /densho/kinkura/working/FILTER_ddr-testing-1.log

5. Move PUBLIC_ddr-testing-1 to /densho/kinkura/public/ddr-testing-1::

    mv /densho/kinkura/working/PUBLIC_ddr-testing-1 /densho/kinkura/public/ddr-testing-1

6. Run ddrpubcopy, pointing output to /densho/kinkura/transfer/ddr-testing-1::

    su ddr
    cd /usr/local/src/ddr-local/ddrlocal
    ddrpubcopy -ma -c /densho/kinkura/public/ddr-testing-1 -d /densho/kinkura/transfer

6. Transfer files from HQ to public storage.

7. Run ddrindex on /densho/kinkura/public/ddr-testing-1, targeting public ElasticSearch server in colo::

    su ddr
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrindex index -H PUBLIC_ES_SERVER:9200 --recursive -i documents \
    -p /densho/kinkura/public/ddr-testing-1 | \ 
    tee /densho/kinkura/working/logs/ddrindex_ddr-testing-1.log
   
   ddrindex can be run against an entire directory with `--recursive` mode selected. 
   
Other Utilities
-------------------------------------

It may be necessary to clobber an entire collection and its child entities into published status. The `ddrmassupdate` script will traverse an entire collection repo and find all `collection.json` and `entity.json` files, modifying the `'status'` attribute to the value `'completed'`. As the developer has stated in the docs::

    WARNING! THIS SCRIPT IS AN EXTREMELY STUPID BLUNT INSTRUMENT!
    
To run the script::

    su root
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrmassupdate -c /PATH/TO/MY/ddr-testing-1
    
Note that you *must* be `root` or have privs to write in the `/usr/local/src/ddr-cmdln/ddr` directory in order to use the script because of the default location of the logfile. Happy clobbering! 


Refresh Public Server
=====================

Workflow for completely replacing data in Elasticsearch, for `ddr-public`.

The following commands must be run on the server on which the repositories reside.  First open a Python interpreter::

    $ su ddr
    [password]
    $ python
    Python 2.7.3 (default, Mar 14 2014, 11:57:14) 
    [GCC 4.7.2] on linux2
    Type "help", "copyright", "credits" or "license" for more information.
    >>> 

Import the necessary libraries, then set variables for your Elasticsearch host and for the index you'll be putting documents in.::

    from DDR import models
    from DDR import docstore
    
    HOSTS = [{'host':'192.168.X.Y', 'port':9200}]
    INDEX = 'documents0'

Next, delete any existing index, create a new index, and upload mappings and facet information.::

    docstore.delete_index(HOSTS, INDEX)
    
    docstore.create_index(HOSTS, INDEX)
    docstore.put_mappings(HOSTS, INDEX, docstore.HARD_CODED_MAPPINGS_PATH, models.MODELS_DIR)
    docstore.put_facets(HOSTS, INDEX, docstore.HARD_CODED_FACETS_PATH)

Set an alias for the index.  This name must match `DOCSTORE_INDEX` in `ddr-public/ddrpublic/ddrpublic/settings.py`.::

    es = docstore._get_connection(HOSTS)
    es.indices.put_alias(index=[INDEX], name='ALIAS', body={})

Each repository and organization must have a corresponding metadata document.  The organization files can be found in the organizations' inventory repositories.::

    # This is still a little clunky.
    import json
    def loads( path ):
        with open(path, 'r') as f:
            data = json.loads(f.read())
        return data
    
    PATH = '/PATH/TO/REPOSITORIES'
    es = docstore._get_connection(HOSTS)
    
    es.index(index=INDEX, doc_type='repository', id='ddr', body=loads('%s/ddr/repository.json' % PATH))
    
    # Do this once per organization.
    es.index(index=INDEX, doc_type='organization', id='ddr-densho', body=loads('%s/REPO-ORG/organization.json' % PATH))

Press `Control-D` to exit the Python interpreter.

Use the `ddrindex` command to upload metadata for each collection repository.::

    $ ddrindex index -H HOST:PORT -r -i INDEX -p /PATH/TO/REPOSITORIES/REPO-ORG-CID
