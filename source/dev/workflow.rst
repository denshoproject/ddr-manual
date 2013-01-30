Workflow
========


Locations
* hq
* colo
* local

Components
* im - Integration Manager repo (git, git-annex)
* sandbox - partner sandbox repos (git, gitolite, git-annex)
* workbench - workbench web app (partner.densho.org/workbench)
* local - web app on partner local Debian/Ubuntu VM
* pubic - 



Repository-level operations
----------

repository create
repo admin user add
    add user key to gitolite
repo admin user edit
repo admin user remove

Organization-level operations
----------

organization create
    add organization to workbench server
    add organization to gitolite-admin (and commit,push)
organization edit
organization remove
    disable organization inactive on workbench server
    disable organization in gitolite-admin (and commit,push)
organization user add
    add user to workbench server
    add user to local django
    generate public/private keys
        # if user DOES NOT HAVE id_rsa.pub:
        # run the following as the user
        $ ssh-keygen
        filename: default (~/.ssh/id_rsa)
    send public key to admins
    add user key to gitolite-admin (and commit,push)
        NOTE: use old style multi-keys (http://sitaramc.github.com/gitolite/users.html#multi-key)
        $ cd $GITOLITEADMIN
        $ mv /tmp/id_rsa.pub keydir/$USER@$HOSTNAME.pub
        # mod conf/gitolite.conf
        $ git add keydir/$USER@$HOSTNAME.pub
        $ git add conf/gitolite.conf
        $ git commit
        $ git push origin master
organization user edit
organization user remove
    remove user from workbench server
    remove user from local django
    remove user key from gitolite-admin (and commit,push)

Collection-level operations
----------

collection create
collection edit
collection remove

collection entity add
collection entity remove

collection sync


Entity-level operations
----------

entity edit

entity file add
entity file batch add
    starts celery proc which calls entity cmd
entity file edit
entity file remove

entity file mezzanine copy generate
entity file mezzanine copy remove
    same as entity file add/rm but with special metadata

entity file access copy generate
entity file access copy remove

entity sync
entity annex sync




Partner user created
    workbench: Create user account
    local: generate ssh key
    local: upload ssh key to workbench
    workbench: add key to gitolite 

Partner creates entity
    # NO
    # local requests UID from workbench API
    # background:entity init: $ [entity -eENTITYUID -oinit]
    # background:entity init: # create entity dir, METS.xml, changelog, etc
    # background:entity init: $ git init
    # background:entity init: $ git annex init
    # background:entity init: $ git commit
    --- OR ---
    # YES.
    # Easier to have Gitolite create repo then clone (http://sitaramc.github.com/gitolite/repos.html)
    # than to add existing to Gitolite (http://sitaramc.github.com/gitolite/rare.html#existing).
    local requests UID from workbench API
    background:entity init: $ entity -eENTITYUID -oinit]
    background:entity init: $ git clone git@mits:ddr-ORG-C-E
        $ git clone git@mits:ddr-densho-1-1
        Cloning into 'ddr-densho-1-1'...
        Initialized empty Git repository in /home/git/repositories/ddr-densho-1-1.git/
        warning: You appear to have cloned an empty repository.
    background:entity init: $ git annex init
    background:entity init: $ git add changelog control mets.xml
    background:entity init: $ git commit

    # 2013-01-29
    # For some reason when I git clone git@mets:...
    # there is no master branch, so I have to do this:
    background:entity init: $ git clone git@mits:ddr-ORG-C-E
        $ git clone git@mits:ddr-densho-1-1
        Cloning into 'ddr-densho-1-1'...
        Initialized empty Git repository in /home/git/repositories/ddr-densho-1-1.git/
        warning: You appear to have cloned an empty repository.
    background:entity init: $ git add changelog control mets.xml
    background:entity init: $ git commit
    # the master branch appears at this point
    background:entity init: $ git annex init



Partner edits metadata


Partner adds a file
    web app accepts upload to tmp dir
    background:entity add: $ entity -eENTITYUID -oadd -f/tmp/FILE]
    background:entity add: $ cp file from /tmp/
    background:entity add: # read file metadata
    background:entity add: # add file metadata to mets.xml
    background:entity add: # generate sm access copy
    background:entity add: $ git annex add files/[file]
    background:entity add: $ git annex add files/[sm access copy]
    background:entity add: $ git commit

Partner removes a file
    web app accepts confirmation
    background:entity rm: $ git annex [rm?] file
    background:entity rm: $ git annex [rm?] mezzanine copy
    background:entity rm: $ git annex [rm?] access copy
    background:entity rm: $ git commit

Partner syncs with sandbox
    local partner clicks sync in webapp
    background:entity sync: $ [entity -eENTITYUID -osync]
    # make sure local repo is up to date
    background:entity sync: $ git fetch origin
    background:entity sync: $ git pull origin master
    background:entity sync: $ git co git-annex
    background:entity sync: $ git pull origin git-annex
    background:entity sync: # may need to merge here
    background:entity sync: $ git co master
    # OK now push
    background:entity sync: $ git push --all
    for f in access copies:
        background:entity sync: $ git annex copy --to=origin [sm access copies]
    
    NOTE: sometimes git push --all fails:
        $ git push --all
        To git@mits:ddr-densho-1-1
         ! [rejected]        git-annex -> git-annex (non-fast-forward)
        error: failed to push some refs to 'git@mits:ddr-densho-1-1'
        hint: Updates were rejected because a pushed branch tip is behind its remote
        hint: counterpart. Check out this branch and merge the remote changes
        hint: (e.g. 'git pull') before pushing again.
        hint: See the 'Note about fast-forwards' in 'git push --help' for details.
     
    To fix this (I think):
        $ git co git-annex
        Switched to branch 'git-annex'
        $ git pull origin git-annex
        From mits:ddr-densho-1-1
         * branch            git-annex  -> FETCH_HEAD
        Auto-merging 4ef/4b4/SHA256-s77616--1fe9c520c9b1d4022cdceb6547e8d9816039320fdfda24c17eac1f428e56c838.log
        CONFLICT (content): Merge conflict in 4ef/4b4/SHA256-s77616--1fe9c520c9b1d4022cdceb6547e8d9816039320fdfda24c17eac1f428e56c838.log
        Automatic merge failed; fix conflicts and then commit the result.
        $ git add 4ef/4b4/SHA256-s77616--1fe9c520c9b1d4022cdceb6547e8d9816039320fdfda24c17eac1f428e56c838.log
        [git-annex 583902e] Merge branch 'git-annex' of mits:ddr-densho-1-1 into git-annex
        $ git co master
        Switched to branch 'master'
     
    On another occasion, this seemed to fix it:
        $ git co git-annex
        Switched to branch 'git-annex'
        $ git pull origin git-annex
        From mits:ddr-densho-1-1
         * branch            git-annex  -> FETCH_HEAD
        Updating 583902e..471f16f
        Fast-forward
         4ef/4b4/SHA256-s77616--1fe9c520c9b1d4022cdceb6547e8d9816039320fdfda24c17eac1f428e56c838.log |    5 +----
         b19/130/SHA256-s38261--a35666f2d6c24c7579a30aa7bd703ef5c7037beae030ef5ff1d5d0edf2f9f948.log |    1 +
         uuid.log                                                                                    |    1 +
         3 files changed, 3 insertions(+), 4 deletions(-)
        $ git co master
        Switched to branch 'master'
        $ git push --all
        Everything up-to-date


VirtualBox/VMware shared folder
VirtualBox/VMware USB peripherals visible in menubar

would be nice to not use the VM HD at all, just use shared folder
except git-annex relies on symlinks -- crap
2nd HD in machine, formatted ext4; entire HD is the shared folder


Partner sends USB HD to 

We need to assume that Git sync ops will be interrupted
we need to figure out how to recover from interruptions
they will happen
