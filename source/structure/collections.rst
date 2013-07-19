Repository Structure - Collections
===========


**TL;DNR:** Description of the collection level of the repository.


File structure
--------------

::

    ddr-densho-1
    ├── changelog
    ├── control
    ├── ead.xml
    ├── files
    │   ├── ddr-densho-1-1
    │   │   ├── changelog
    │   │   ├── control
    │   │   ├── files
    │   │   │   ├── image1.jpg -> ../../../.git/annex/objects/...
    │   │   │   └── image2.jpg -> ../../../.git/annex/objects/...
    │   │   └── mets.xml
    │   │    ...
    │   └── ddr-densho-1-N
    │       ├── changelog
    │       ├── control
    │       ├── files
    │       │   ├── image1.jpg -> ../../../.git/annex/objects/...
    │       │   └── image2.jpg -> ../../../.git/annex/objects/...
    │       └── mets.xml
    ├── .git
    │   ├── annex
    │   ├── ...
    │   ├── config
    │   └── ...
    └── .gitignore


Metadata
--------

Collections are described using EAD.


Sample EAD.xml
--------------

::

    <ead>
      <eadheader>
        <eadid/>
        <filedesc/>
        <profiledesc/>
      </eadheader>
      <archdesc>
        <did/>
      </archdesc>
      <bioghist/>
      <scopecontent/>
      <relatedmaterial/>
      <separatedmaterial/>
      <controlaccess/>
      <accessrestrict/>
      <userestrict/>
      <dsc type="combined"><head>Inventory</head>
        <c01>
          <did>
           <unittitle>Joe Tanaka video interview</unittitle>
           <container type="box">1</container>
           <container type="folder">1</container>
          </did>
        </c01>
      </dsc>
    </ead>
