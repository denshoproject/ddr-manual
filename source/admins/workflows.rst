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

At Densho HQ, using "ddr-testing-1" example collection repo:

1. Move/copy ddr-testing-1 from import staging to /densho/kinkura/gold/ddr-testing-1::

    mv /densho/drstores/ddr1/ddr-testing-1 /densho/kinkura/gold/ddr-testing-1

2. Review and approve using ddr-local webui.
3. Run ddrfilter, pointing output to /densho/kinkura/working::

    su ddr
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrfilter -ma -s /densho/kinkura/gold/ddr-testing-1 -d /densho/kinkura/working/ddr-testing-1

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
    ./bin/ddrindex index -H localhost:9200 --recursive -i documents -p /densho/kinkura/public/ddr-testing-1
