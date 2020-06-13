# Notes

## Commands
- Provide sha1 of current blob - echo 'Hello World' | git hash-object --stdin
- Type of commit - git cat-file -t 2ec19b5
- Print contents of commit - git cat-file -p 2ec19b5
- See all commits - git log --oneline
- Setup editor to VS Code - git config --global core.editor "code --wait"

## Git Foundations
- What is git? - a distributed version control system.
- At its core, it is a key value store.
- Key is a sha1 - a cryptographic hash function.

### Git Blobs & Trees
- Blob - git stores compressed data in a blob, along with metadata in the header.
- Git stores sha1 objects in .git directory. 
- tree .git - displays git objects of the repository. Contain pointers to sha1 & blobs and pointers to other trees (since they can be nested).
- Blobs do not have filenames or directory structures. Git stores this information in the tree.
- Trees point to trees and blobs.
- Git optimizations
    - Git objects are compressed
    - Git optimizes for files staying mostly similar
    - Git compresses files into a Packfile
    - The Packfile stores the object, the deltas, or the differences between one version of the file and the next
    - Packfiles are created when you have too many objects, during garbage collection, or during a push to a remote

### Git Commits
- A commit points to a tree and contains author and committer, a date when created, a message, and parent commit (one or more). A sha1 is a hash of all this information.
- A commit is a code snapshot and a combination of the changes from staging area and previous commit.
- When make a commit, first line of status is the hash of that commit.
- Can't change commits. Can't rebase, edit, change author, etc. If you change any data about commit, it will have a new sha1. This is a security feature. 
- References are pointers to commits - tags, branches, and HEAD. HEAD is a pointer to the current commit.
- Changing branches in git is fast because it's not pulling down data, it's changing to a new pointer.
- HEAD is *usually* a pointer to the current branch.

### Git Commit Exercise
- ./exercises/Exercise1-SingleCommit.md
- git.io/config-editor
- git.io/use-less
- Setup editor to VS Code - git config --global core.editor "code --wait"

## Git Areas and Staging
- Three areas where code lives - working area, staging area, and repository

### Working Area, Staging Area, Repository
- Working area - files that are not in staging area, not handled by git, in local directory. Also untracked files. Scratch space, add content, modify or delete content, if in repository, won't lose work.

- Staging area - files are going to be part of next commit. How git knows what will change between current commit and the next one. AKA the index or cache.
    - A "clean" staging area is not empty. The baseline staging area is copy of latest commit. Contains list of files as well as sha1 hash of the files of the last commit.
    - Way it is stored - index is a binary file in .git directory. 
    - Add, remove, rename files in staging area, git knows because sha1 of staged files is different than sha1 that was in the repository. 
    - To look at index use the following plumbing command - git ls-files -s
        - Output - mode, sha1, followed by file name
    - Move files in and out of the staging area
        - Add file to next commit - git add <file>
        - Delete a file in next commit - git rm <file>
        - Rename a file in next commit - git mv <file>
        - Allows for staging commits in huge chunk - git add -p
            - Especially useful if you've done too much work for one commit
        - Commit all changes from working area to repository - git commit -a
            - *Not recommended*
        - When unstaging files from staging area back to work area, you are not removing them, you are just replacing them with a copy that is currently in the repository.
    - Stashing - way to save uncommitted work. Pretty safe from most destructive operations.
        - Handy for switching between branches while in the middle of work.
        - Using operations that can delete or overwrite your work - git reset or git checkout.
            - Stash changes - git stash
            - List changes - git stash list
            - Show the contents - git stash show stash@{0}
            - Apply last stash - git stash apply
            - Apply a specific stash - git stash apply stash@{0}

- Repository - files git knows about. Contains all your commits. In your .git directory. Continue to make changes as you work and can always checkout a fresh copy. 