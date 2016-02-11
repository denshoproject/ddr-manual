.. _guide:

=========================
DDR Workflows
=========================

This page documents the data workflows in the DDR system.

.. contents::
.. section-numbering::



Infrastructure
====================


Set up a user on the Ansible server
--------------------

Refer to the `README` document in the `ansible-colo` repository for complete setup information.


Run package updates on public servers
--------------------

Ansible automates the process of running package updates/upgrades on all servers at once.

Update each VM's copy of the Debian packages::

    $ ansible all -m command -a "apt-get update" -sK

See which packages will be upgraded but don't change anything::
    
    $ ansible all -m command -a "apt-get upgrade -s" -sK

Upgrade packages (`-y` means "assume Yes to all queries and do not prompt")::
  
    $ ansible all -m command -a "apt-get upgrade -y" -sK

The `-sK` flags are short of `--sudo --ask-pass`, which tells Ansible to run the playbook under `sudo` and ask you for the `ansible` user's password at runtime.


Run Ansible playbooks
--------------------

::
   
    $ ansible-playbook -sK PLAYBOOK.yml

The `-sK` flags are short of `--sudo --ask-pass`, which tells Ansible to run the playbook under `sudo` and ask you for the `ansible` user's password at runtime.


Repository
====================


Choose a keyword
--------------------

Choose a single lowercase word or acronym (i.e. no spaces).  This will get used everywhere!  An example:

* ddr


TODO Set up a `gitolite-admin` repo
-----------------------

Gitolite configuration files are kept in a Git repo.  Changing these configurations is a process of pulling, editing, and pushing to the Gitolite server.  See the  the `gitolite administration page`_ for more info.

.. _`gitolite administration page`: http://gitolite.com/gitolite/admin.html


TODO Set up a Repository repo
-----------------------



Organizations
=======================

Information about each DDR partner -- called an Organization in the system -- is stored in a git repo. These repos are always named using the DDR ID convention::

    ddr-[PartnerID]
    E.g., "ddr-densho"

  
Choose a keyword
--------------------

Choose a single lowercase word or acronym (i.e. no spaces).  This will get used everywhere!  Some examples:

* ddr-densho
* ddr-hmwf
* ddr-janm


TODO Add organization to `gitolite-admin` repo.
-----------------------

* Add organization repo access rules to gitolite config on mits


Set up an organization repo
-----------------------

#. Find an existing organization repo.  If you can't find one, see the *Organization* page under *Repository Structure*.
#. Copy the `organization.json` file into a new directory.  Do not copy the `.git` directory or any Store files.
#. Modify the files to suit the new organization.
#. Initialize a new Git repo and make the initial commit.::
     
     $ cd REPO-ORG/
     $ git init
     # git add organization.json
     $ git commit -m "initial commit"

#. Clone a bare copy of the repo.::

     $ cd ..
     $ git clone --bare REPO-ORG/ REPO-ORG.git

#. Upload the bare repo to the Hub server AKA `mits`, put in the `repositories` directory and set the proper permissions.::

     USER@local $ scp -r REPO-ORG.git USER@mits.densho.org:/tmp/
     USER@local $ ssh USER@mits.densho.org
     USER@mits $ sudo cp -R /tmp/REPO-ORG.git /home/git/repositories/
     USER@mits $ sudo chown -R git.git /home/git/repositories/REPO-ORG.git
     USER@mits $ sudo su - git
     git@mits:~$ 
     # recursively chmod directories and files
     git@mits:~$ cd /home/git/repositories/
     git@mits:~/repositories$ for i in `find . -type d`; do chmod 750 $i; done
     git@mits:~/repositories$ for i in `find . -type f`; do chmod 640 $i; done

The organization repo should now be ready for use.  See the Store section for how to clone organization repos to a particular Store.


TODO Clone organization repo to Store
-----------------------


Add organization to ID service
-----------------------

In order to automatically generate Collection and Object IDs across the distributed archive the ID service must have a record for each partner organization.

#. Visit the ID serice site (http://partner.densho.org/workbench/).
#. Log in as a user with admin privileges.
#. Go to the admin page (http://partner.densho.org/workbench/admin/). Note that there is currently no link to this page; you have to cut-and-paste the URL or type it in the URL field of your browser.
#. Go to the *Groups* page. Confirm that the new group is not listed there.
#. Click *Add*.
#. Enter the ORG part of the organization ID (e.g. "densho", "hmwf"), leave the Permissions field blank, and click "Save".

In order for users to be able to get new collection and object IDs, they must be added to the organization group.  Please see the User section.


TODO Add organization to ddr-public
------------------------------

* [`ddr-public`] Add the organization record to the production ElasticSearch index.
* [`ddr-public`] Add a subdir containing the organization icon to production nginx media server store. (i.e., `tulie:/var/www/media/base/`)



Workstations
=======================


TODO Add key to `gitolite-admin`.
--------------------

The `gitolite-admin` repository governs which *SSH keys* have access to which Git repositories.  Please see the 

In order to use Gitolite as a normal or admin user, the following must be true:

- The `/home/ddr/.ssh/` directory on the user's workstation VM must contain an SSH keypair,
- The workstation's **public** key must be present in the `gitolite-admin` repository, and
- The user's username must be present in the `gitolite.conf` file.

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



Users
=======================

Every addition and change to the repository is performed by one user or another.  


Add user to ID service
--------------------

Users must be registered with and logged in to the ID service in order to request new Collection and Object IDs.  The ID service stores their real name and email address.  When they log in to the ID service, this information is remembered by the local Editor application.  Their name and email address is recorded in the `changelog` and `Git commits` or every action they perform.

#. Visit the ID serice site (http://partner.densho.org/workbench/).
#. Log in as a user with admin privileges.
#. Go to the admin page (http://partner.densho.org/workbench/admin/). Note that there is currently no link to this page; you have to cut-and-paste the URL or type it in the URL field of your browser.
#. Go to the *Users* page. Confirm that the new user is not listed there.
#. Click *Add*.
#. Enter their Personal Info, check the box to mark them as Active, assign Staff or Superuser status as needed, and add them to the appropriate group(s).  Leave the User Permissions settings blank.  Click "Save".

NOTE: The ID service only governs the organizations to which users can add new Collection and Object IDs.  Access to Collection repositories is governed by `gitolite-admin`.




Stores
=======================


TODO Create a new Store
-----------------------

HDD
USB


TODO Clone a repository repo
-----------------------


TODO Clone organization repo(s)
-----------------------


TODO Update repository and organization repos
-----------------------



Collections
=======================


Collection Repos
-------------------------------------------

The basic content unit of the DDR system is the Collection, which is instantiated as a git repo. The git repo holds a structured directory of metadata text files as well as the git annex repo info and -- in some cases -- the annex file binary data itself. 

DDR Collection repos are always named using the DDR ID convention::

    ddr-[PartnerID]-[CollectionIDPart]
    E.g., "ddr-densho-2"


Create new collection repo
--------------------

To create a new Collection using the web editor:

#. Log in to the web editor.
#. Make sure you are logged in.
#. Make sure you have a Store mounted.
#. Go to the "Collections" list page (http://192.168.56.101/ui/collections/).
#. Click the "New Collection" button under the appropriate partner heading.
#. A new Collection will be created on the Hub server AKA `mits` and then cloned to your local Store.

Creating a new Collection using the command-line is similar, except that there is currently a bug.  Open a terminal window, SSH in to the VM.::

    $ sudo su ddr
    $ cd /var/www/media/ddr/
    $ ddr create -u USER -m MAIL \
      -t /usr/local/src/ddr-cmdln/ddr/DDR/templates \
      -c /var/www/media/ddr/REPO-ORG-CID/


Clone collection repo to Store
--------------------

To clone an existing Collection:

#. Log in to the web editor.
#. Make sure you are logged in.
#. Make sure you have a Store mounted.
#. Log in to your workstation VM.
#. Enter the following at the command line.::

    $ sudo su ddr
    $ cd /var/www/media/ddr/
    $ ddr clone --user USER --mail MAIL --cid REPO-ORG-CID \
      --dest /var/www/media/ddr/REPO-ORG-CID


TODO Import Entities and Files
--------------------

The standard method for working with the DDR is through the ddr-local web ui; however, it is also possible to create new Entities and Files using the manual batch import scripts. 

The commands are available with `ddr-cmdln` and `ddr-local` installed. Both should be on the `master` branch.

To use the Entities importer:

#. Make certain the target Collection repo is located where the VM can access it. The Collection repo must already exist!
#. Prep a valid Entities CSV file and place in a directory that the VM can access. A valid import file must be well-formed CSV that contains the following headers::

    id,status,public,title,description,creation,location,creators,language,genre,format,extent,contributors,alternate_id,digitize_person,digitize_organization,credit,topics,persons,facilities,parent,rights,rights_statement,notes

#. Log into a command-line session as the `ddr` user and start an interactive python session.::

    su ddr
    cd /usr/local/src/ddr-local/ddrlocal
    ./manage.py shell -i bpython
   
#. In the python shell, run the importer method.::

    from migration import densho
    user='Your Name'
    mail='your.email@densho.org'
    collection='/PATH/TO/ddr-repo-name
    csv='/PATH/TO/ddr-repo-name-entities-data.csv'
    densho.import_entities(csv,collection,user,mail)
    
#. The importer will send status messages for each entity create operation to the screen; you can capture the terminal output and log if necessary.

To use the Files importer:

#. Prep valid CSV file and place in a directory with the import binaries that the VM can access.
#. Make certain the target Collection repo is located where the VM can access it. The Collection repo and any Entity to which you would like to attach Files must already exist!
#. Prep a valid Files CSV file and place in a directory that the VM can access. A valid import file must be well-formed CSV that contains the following headers: ::

    entity_id,file,role,public,rights,digitize_person,tech_notes,label,sort
   
#. Log into a command-line session as the `ddr` user and start an interactive python session.::

    su ddr
    cd /usr/local/src/ddr-local/ddrlocal
    ./manage.py shell -i bpython
   
#. In the python shell, run the importer method.::

    from migration import densho
    user='Your Name'
    mail='your.email@densho.org'
    collection='/PATH/TO/ddr-repo-name
    csv='/PATH/TO/ddr-repo-name-files-data.csv'
    densho.import_files(csv,collection,user,mail)
    
#. The importer will send status messages for each entity create operation to the screen; you can capture the terminal output and log if necessary.

Merging Partner Binary Content
-------------------------------------------
Because of the size of the binary content, it is not feasible to transfer the binary content in the git-annexes directly over the network from remote sites. When binaries are ingested into the DDR system, they are stored in a local git-annex, usually located on the workstation itself. Upon syncing the collection, other repo clones -- i.e., on mits.densho.org and in the Densho HQ, know of the existence of each binary and of their respective checksums; but in order for binary content to be preserved on the Densho infrastructure and published to the DDR public site, it must be transferred to the Seattle HQ. 

Using an ext3 or ext4 formatted, empty USB drive at the remote site:

1. Connect the USB hdd used for transfer to the local VM
2. Capture USB device in VirtualBox. Devices-->USB Devices-->[drive name]
3. Mount USB to local share. E.g.: ::

    sudo pmount /dev/sdb1 /media/usbhdd

4. Make a directory on the drive where the transfer repo(s) will reside.::

    sudo mkdir /media/usbhdd/ddr
    
5. As the ddr user, clone the desired collection repo to the USB::

    cd /media/usbhdd/ddr
    git clone git@mits.densho.org:ddr-testing-1.git

6. Create a git-annex for the usb transfer repo::

    cd /media/usbhdd/ddr/ddr-testing-1
    git annex init "usb-transfer-1"

7. Navigate to the transfer repo and add the existing collection repo as a remote::

    cd /media/nfs/gold/ddr-testing-1
    git remote add ddr-testing-local /media/ddrstore/ddr/ddr-testing-1
   
8. Pull the binary content into the transfer annex::

    git annex get .


Upon receipt of USB hdd at Densho (and after making a local backup of usb data):

1. Connect to local instance of ddr-local VM
2. Capture USB device in VirtualBox. Devices-->USB Devices-->[drive name]
3. Mount USB to local share. E.g.: ::

    sudo pmount /dev/sdb1 /media/usbhdd

4. Navigate to local collection repo and add remote on usb drive::

    cd /media/nfs/gold/ddr-testing-1
    git remote add usb-transfer-1 /media/nfs/gold/ddr-testing-1
   
5. Pull the binary content into the local annex::

    git annex get .

6. Remove the usb remote from the local repo::

    git remote rm usb-transfer-1

TODO Prepare Collections for publication
-------------------------------------------

The following details the procedure for publishing completed Collection repos. This is specific to the archival processes and operational environment of the DDR project at Densho. 

The commands are available with `ddr-cmdln` and `ddr-local` installed. Both should be on the `master` branch.

At Densho HQ, using `ddr-testing-1` example collection repo:

#. Move/copy `ddr-testing-1` from import staging to `/densho/kinkura/gold/ddr-testing-1`::

    mv /densho/drstores/ddr1/ddr-testing-1 /densho/kinkura/gold/ddr-testing-1

#. Review and approve using ddr-local webui.
#. Run `ddrfilter`, pointing output to `/densho/kinkura/working`::

    su ddr
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrfilter --keeptmp --mezzanine --access \
      --source /densho/kinkura/gold/ddr-testing-1 \
      --destdir /densho/kinkura/working

   Result::
    
    ddr@kinkura:/densho/kinkura/working# ls
    FILTER_ddr-densho-testing-1
    FILTER_ddr-densho-testing-1.log
    FILTER_ddr-densho-testing-1.sh
    
#. Run the generated filtering script::

    sh /densho/kinkura/working/FILTER_ddr-densho-testing-1.sh | tee -a /densho/kinkura/working/FILTER_ddr-testing-1.log

#. Move `PUBLIC_ddr-testing-1` to `/densho/kinkura/public/ddr-testing-1`::

    mv /densho/kinkura/working/PUBLIC_ddr-testing-1 /densho/kinkura/public/ddr-testing-1

#. Run `ddrpubcopy`, pointing output to `/densho/kinkura/transfer/ddr-testing-1`::

    su ddr
    cd /usr/local/src/ddr-local/ddrlocal
    ddrpubcopy --mezzanine --access \
      --collection /densho/kinkura/public/ddr-testing-1 \
      --destbase /densho/kinkura/transfer

#. Transfer files from HQ to public storage.

#. Run `ddr-index` on `/densho/kinkura/public/ddr-testing-1`, targeting public ElasticSearch server in colo::

    su ddr
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrindex index -H PUBLIC_ES_SERVER:9200 --recursive -i ddrpublic-production \
    -p /densho/kinkura/public/ddr-testing-1 | \ 
    tee -a /densho/kinkura/working/logs/ddrindex_ddr-testing-1.log
   
   ddrindex can be run against an entire directory with `--recursive` mode selected. 
   (NOTE: The index name for ddrstage is 'stage'.)


Other Utilities
-------------------------------------

It may be necessary to clobber an entire collection and its child entities into published status. The `ddrmassupdate` script will traverse an entire collection repo and find all `collection.json` and `entity.json` files, modifying the `'status'` attribute to the value `'completed'`. As the developer has stated in the docs::

    WARNING! THIS SCRIPT IS AN EXTREMELY STUPID BLUNT INSTRUMENT!
    
To run the script::

    su root
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrmassupdate -c /PATH/TO/MY/ddr-testing-1
    
Note that you *must* be `root` or have privs to write in the `/usr/local/src/ddr-cmdln/ddr` directory in order to use the script because of the default location of the logfile. Happy clobbering! 


[develop (new)] Publish collection repos to public server
--------------------

NEW Workflow for completely replacing data in Elasticsearch, for `ddr-public`.

List indexes and aliases, and get status info for specified index.::
  
    $ ddr-index status -H localhost:9200 -i documents

Delete an existing index.::
  
    $ ddr-index remove -H localhost:9200 -i documents

Initialize a new index.  This step creates the index and uploads mappings and facet information.::
      
    $ ddr-index init -H localhost:9200 -i documents /var/www/media/ddr/ddr

Set an alias for the index.  This name must match `DOCSTORE_INDEX` in `ddr-public/ddrpublic/ddrpublic/settings.py`.::
      
    $ ddr-index alias -H localhost:9200 -i documents -a ddrpublic-stage

Each repository and organization must have a corresponding metadata document.  The organization files can be found in the organizations' inventory repositories.::
    
    $ ddr-index repo -H localhost:9200 -i documents /var/www/media/ddr/REPO/repository.json
    $ ddr-index org -H localhost:9200 -i documents /var/www/media/ddr/REPO-ORG/organization.json
    
Upload metadata for each collection repository.::
      
    $ ddr-index index -H localhost:9200 -i documents --recursive --newstyle /var/www/media/ddr

Complete usage information is available from the `ddr-index` command itself.::

    $ ddr-index --help


[master (old)] Publish collection repos to public server
--------------------

OLD Workflow for completely replacing data in Elasticsearch, for `ddr-public`.

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
    docstore.put_mappings(HOSTS, INDEX, docstore.MAPPINGS_PATH, models.MODELS_DIR)
    docstore.put_facets(HOSTS, INDEX, docstore.FACETS_PATH)

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


Controlled Vocabularies
=======================

The DDR supports several controlled vocabularies that provide standard values for several attributes in collection, entity and file metadata.

Currently there are two vocabularies:

- topics
- facility

Vocabulary data resides in the following repositories:

- `git@github.com:densho/ddr-vocab <https://github.com/densho/ddr-vocab>`_
- `git@mits.densho.org:ddr.git <http://partner.densho.org/cgit/cgit.cgi/ddr>`_

Updating
--------------------

Clone the `ddr-vocab` repository, replace `$(VOCAB).json`, commit, and push to GitHub.::

  $ git clone git@github.com:densho/ddr-vocab
  $ cd ddr-vocab
  [edit api/0.2/$VOCAB.json]
  $ git add -p api/0.2/$VOCAB.json
  $ git commit
  $ git push

Clone the `ddr` repository, replace `$(VOCAB).json`, commit, and push to Gitolite server (`mits2`).::

  $ git clone git@mits.densho.org:ddr.git
  $ cd ddr
  [edit vocab/$VOCAB.json]
  $ git add -p vocab/$VOCAB.json
  $ git commit
  $ git push

Pull `ddr-vocab` repository to the workbench/vocabs API server (`schoolboy`) by running the Ansible playbook `ddrwkb`.  See "Run Ansible playbooks" section above.

Pull `ddr` repository to `ddr-public` production/stage servers by running the Ansible playbook `ddrpub`.  See "Run Ansible playbooks" section above.

Pull `ddr` repository to any ddr integration VM (i.e., `kinkura`) that will be running `ddr-index`.

Run `ddr-index` against production Elasticsearch to refresh existing facets.
