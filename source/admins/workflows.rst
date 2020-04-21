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

#. Visit the ID service site (https://idservice.densho.org/).
#. Log in as a user with admin privileges.
#. Go to the admin page (https://idservice.densho.org/admin/). Note that there is currently no link to this page; you have to cut-and-paste the URL or type it in the URL field of your browser.
#. Go to the *Groups* page. Confirm that the new group is not listed there.
#. Click *Add*.
#. Enter the ORG part of the organization ID (e.g. "densho", "hmwf"), leave the Permissions field blank, and click "Save".

In order for users to be able to get new collection and object IDs, they must be added to the organization group.  Please see the User section.


Add organization to ddr-public
------------------------------

To support searching and filtering by organization ("partner") in `ddr-public`, the following procedures must be performed:

#. Index the organization record into the production ElasticSearch index.::

    ddrindex org -h [DOCSTORE_HOSTS] /path/to/[ORG_REPO]/organization.json

#. Add a subdir containing the organization's icon image binary to production nginx media server store. (e.g., `tulie:/var/www/media/base/`)


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

#. Visit the ID service site (https://idservice.densho.org/).
#. Log in as a user with admin privileges.
#. Go to the admin page (https://idservice.densho.org/admin/). Note that there is currently no link to this page; you have to cut-and-paste the URL or type it in the URL field of your browser.
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
      -t [/etc/ddr/ddr-defs/templates/ead.xml] \
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


Import Entities and Files
--------------------

The standard method for working with the DDR is through the ddr-local web ui; however, it is also possible to create new Entities and Files using the manual batch import scripts. 

The commands are available with `ddr-cmdln` and `ddr-local` installed. Both should be on the `master` branch.

**To EXPORT objects or files:**

#. Make certain the target Collection repo is located where the VM can access it. The Collection repo must already exist!
#. Log into a command-line session as the `ddr` user and generate a blank Entities CSV file.  Replace `OBJECTTYPE` with the type of object (`entity` or `file`).  Replace paths with the correct paths to the collection repository and to the file you wish to create.::

    $ sudo su ddr
    $ ddr-export --blank OBJECTTYPE /PATH/TO/ddr-repo-name /PATH/ddr-repo-name-entities.csv

**To IMPORT objects or files:**

#. Place the CSV file in a directory that the VM can access.
#. Log into a command-line session as the `ddr` user.  Replace `OBJECTTYPE` with the type of object (`entity` or `file`).  Replace paths with the correct paths to the collection repository and to the file you wish to create.  `ddr-import` will access the ID service so replace `IDSERVICE_USER` and `IDSERVICE_PASSWORD` with valid ID service credentials.  Use the `--dryrun` flag to read the CSV file and create objects but not modify any files.::

    $ sudo su ddr
    $ ddr-import OBJECTTYPE -U IDSERVICE_USER -P IDSERVICE_PASSWORD /PATH/ddr-repo-name-entities.csv /PATH/TO/ddr-repo-name

#. The importer will print status messages for each entity create operation to the screen.

#. If you have imported objects AKA entities you need to register the IDs with the ID service so that adding new objects does not cause and ID conflict.  Replace example values as in previous steps.  Use the `--dryrun` flag to see which IDs will be created (and to see if there are any conflicts) without actually changing information on the ID service.::

    $ sudo su ddr
    $ ddr-import register -U IDSERVICE_USER -P IDSERVICE_PASSWORD /PATH/ddr-repo-name-entities.csv /PATH/TO/ddr-repo-name

#. Print help information using the `--help` flag.::

    $ sudo su ddr
    $ ddr-import --help

#. **IMPORTANT** `ddr-import` will modify and stage your files but will not actually commit them!  Remember to commit your changes once you are satisfied with your changes.::

    $ sudo su ddr
    $ cd /PATH/ddr-repo-name-entities.csv /PATH/TO/ddr-repo-name
    $ git commit -m "DESCRIBE YOUR CHANGES HERE"


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

Preparing `ddr-public` Elasticsearch cluster
--------------------

These steps outline the process for using `ddrindex` tools to prepare an Elasticsearch cluster to hold `ddr-public` data. They are only necessary if the `ddr-public` indices do not already exist, and require that the `ddr-cmdln`, `ddr-local` and `ddr-defs` repos are present, and running on the `master` branch. This procedure assumes that the `ddr-public`-compatible version of Elasticsearch is installed (currently 7.3.1), and that the cluster is already up and running.

#. Create the DDR indices.  This step initializes the indices and uploads controlled vocabularies and narrator information. Note that the ESHOST_IP must match the `docstore_host` var in both the `[local]` and `[public]` sections of `/etc/ddr/ddrlocal.cfg` or the overrides in `/etc/ddr/ddrlocal-local.cfg`::
      
    $ ddrindex create -h HOST:PORT
    $ ddrindex vocabs -h HOST:PORT /opt/ddr-local/ddr-vocab/api/0.2/
    $ ddrindex narrators -h HOST:PORT /opt/ddr-cmdln/densho-vocab/api/0.2/narrators.json

You can check if the DDR index already exists with the following command::

    $ ddrindex status -h HOST:PORT

You can also delete the existing indices, if you need to completely re-initialize the ES cluster for `ddr-public`.::
  
    $ ddrindex destroy -h HOST:PORT
    
#. Set up repository and organization documents. The 'repository' data is basic information about the `ddr-public` instance, and the 'organization' json documents describe each individual DDR partner. Partner content will not be displayed by the site until the the corresponding 'organization' json document is indexed. The 'organization' files can be found in the organizations' inventory repositories; the master 'repository' json is in the 'ddr' repo::
    
    $ ddrindex repo -h HOST:PORT /PATH/TO/ddr/repository.json
    $ ddrindex org -h HOST:PORT /PATH/TO/REPO-ORG/organization.json

The ES cluster is now ready to accept DDR collection data. 

Prepare Collections for publication
-------------------------------------------

The following details the procedure for publishing completed Collection repos. This is specific to the archival processes and operational environment of the DDR project at Densho. 

The commands are available with `ddr-cmdln`, `ddr-local`, and `ddr-defs` installed. All should be on the `master` branch.   

At Densho HQ, using `ddr-testing-1` example collection repo:

#. Move/copy `ddr-testing-1` from import staging to `/densho/kinkura/gold/ddr-testing-1`::

    mv /densho/drstores/ddr1/ddr-testing-1 /densho/kinkura/gold/ddr-testing-1

#. Review and approve using ddr-local webui.
#. [OPTIONAL] Run `ddr-filter`, pointing output to `/densho/kinkura/working`::

    su ddr
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddr-filter --keeptmp --mezzanine --access \
      --source /densho/kinkura/gold/ddr-testing-1 \
      --destdir /densho/kinkura/working

   Result::
    
    ddr@kinkura:/densho/kinkura/working# ls
    FILTER_ddr-densho-testing-1
    FILTER_ddr-densho-testing-1.log
    FILTER_ddr-densho-testing-1.sh
    
#. [OPTIONAL] Run the generated filtering script::

    sh /densho/kinkura/working/FILTER_ddr-densho-testing-1.sh | tee -a /densho/kinkura/working/FILTER_ddr-testing-1.log

#. [OPTIONAL] Move `PUBLIC_ddr-testing-1` to `/densho/kinkura/public/ddr-testing-1`::

    mv /densho/kinkura/working/PUBLIC_ddr-testing-1 /densho/kinkura/public/ddr-testing-1

#. Run `ddr-pubcopy`, pointing output to `/densho/kinkura/transfer/ddr-testing-1`::

    su ddr
    cd /usr/local/src/ddr-local/ddrlocal
    ddr-pubcopy --mezzanine --access --transcript \
       /densho/kinkura/public/ddr-testing-1 \
       /densho/kinkura/transfer

#. Transfer files from HQ to public storage.

#. Run `ddrindex` on `/densho/kinkura/public/ddr-testing-1`, targeting public ElasticSearch server in colo::

    su ddr
    cd /usr/local/src/ddr-cmdln/ddr
    ./bin/ddrindex publish -h PUBLIC_HOST:9200 --recurse \
      /densho/kinkura/public/ddr-testing-1 | \ 
    tee -a /densho/kinkura/working/logs/ddrindex_ddr-testing-1.log
   
   ddrindex can be run against an entire directory with `--recurse` mode selected. 
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

Controlled Vocabularies
=======================

The DDR supports several controlled vocabularies that provide standard values for several attributes in collection, entity and file metadata.

Currently there are two vocabularies:

- topics
- facility

Vocabulary data resides in the following repositories:

- `git@github.com:densho/ddr-vocab <https://github.com/densho/ddr-vocab>`_
- `git@mits.densho.org:ddr.git <http://partner.densho.org/cgit/cgit.cgi/ddr>`_

See :doc:`controlled-vocabs` for more info.


Updating Controlled Vocabs #0
--------------------

Download from Google Docs: `File > Download as > Comma-separated values (.CSV)`

Edit so spreadsheet has following headers::

  - id
  - _title
  - title
  - parent_id
  - weight
  - created
  - modified
  - encyc_urls
  - description

Edit first three lines, before headers start::

  'id','topics'
  'title','Topics'
  'description','DDR Topics'

Put the .CSV file someplace where it can be read by DDR (see *clone* section of *Updating Controlled Vocabs #1*).

Generate new `$VOCAB.json` file::

  $ cd /opt/ddr-local/venv/ddrlocal
  $ source /opt/ddr-local/venv/ddrlocal/bin/activate
  $ python manage.py shell
  >>> from DDR import vocab
  >>> index = vocab.Index()
  >>> index.read('/PATH/TO/OFFICIAL-DenshoTopicsForImport - New Topics.csv')
  >>> index.write('/PATH/TO/ddr-vocab/api/0.2/topics.json')


Updating Controlled Vocabs #1
--------------------

When adding, removing, or otherwise modifying vocab files, run following in the Python console to regenerate the ancestor/sibling/children links.::

  $ cd /opt/ddr-local/venv/ddrlocal
  $ source /opt/ddr-local/venv/ddrlocal/bin/activate
  $ python manage.py shell
  >>> from DDR import vocab
  >>> index = vocab.Index()
  >>> path = '/PATH/TO/topics.json'
  >>> index.read(path)
  >>> index.write(path)

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

Pull `ddr` repository to any ddr integration VM (i.e., `kinkura`) that will be running `ddrindex`.

Run `ddrindex` against production Elasticsearch to refresh existing facets.
