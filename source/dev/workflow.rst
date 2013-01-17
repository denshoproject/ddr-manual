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
organization edit
organization remove
organization user add
    add user key to gitolite
organization user edit
organization user remove

Collection-level operations
----------

collection create
collection edit
collection remove

Entity-level operations
----------

entity create
entity edit
entity remove

entity file add
entity file edit
entity file remove

entity file mezzanine copy generate
entity file mezzanine copy remove

entity file access copy generate
entity file access copy remove

entity push
entity annex sync




Partner user created
    workbench: Create user account
    local: generate ssh key
    local: upload ssh key to workbench
    workbench: add key to gitolite 

Partner creates entity
    local requests UID from workbench API
    background:entity init: $ [entity -eENTITYUID -oinit]
    background:entity init: # create entity dir, METS.xml, changelog, etc
    background:entity init: $ git init
    background:entity init: $ git annex init
    background:entity init: $ git commit
    --- OR ---
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


Partner sends USB HD to 
