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
    ./bin/ddrindex index -H PUBLIC_ES_SERVER:9200 --newstyle --recursive -i documents \
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
