# Notes

## Commands
- Provide sha1 of current blob - echo 'Hello World' | git hash-object --stdin
- Type of commit - git cat-file -t 2ec19b5
- Print contents of commit - git cat-file -p 2ec19b5
- See all commits - git log --oneline
- Setup editor to VS Code - git config --global core.editor "code --wait"
- Git add & commit one line - git add-commit -m 'My commit message'
- Switch to previous branch - git checkout -

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
            - Keep untracked files - git stash --include-untracked
            - Keep all files, even ignored ones, use with caution, files stay untracked, one of the most useful/most used commands - git stash --all
            - Name stashes for easy references - git stash save "Work in progress"
            - Start a new branch from a stash - git stash branch <optional branch name>
            - Grab a single file fromm a stash - git checkout <stash name> -- <filename>
            - Remove last stash and apply changes (doesn't remove if merge conflict) - git stash pop
            - Remove last stash - git stash drop
            - Remove the nth stash - git stash drop stash@{n}
            - Remove all stashes - git stash clear
            - Selective changes - git stash -p

- Repository - files git knows about. Contains all your commits. In your .git directory. Continue to make changes as you work and can always checkout a fresh copy. 

## References, Commits, Branches
- There are three types of git references - tags & annotated tags, branches, HEAD

### References
- References are pointers to commits.
- Branches is a pointer to a particular reference.
- The pointer of the current branch changes as new commits are made.
- HEAD is how git knows what branch you are current on. HEAD is how git knows what the next parent will be.
- HEAD is a pointer that points to the name of the current branch. It can point to a commit too which puts us in a detached HEAD state. 
- HEAD moves when you make a commit to an active branch or checkout a new branch. 

### Tags & Annotated Tags
- Lightweight tags are simple pointer to commit. Created when a tag is created with no arguments and captures value of HEAD.
- Annotated tags - git tag -a - they point to a commit and store additional info including author and date.
- List all tags in a rep - git tag
- List all tags and what repo they are point to - git show-ref --tags
- List all the tags point to a commit - git tag --points-at <commit>
- Looking at the tag or tagged item - git show <tag-name>
- Tags & branches - two separators - use branch when new commits will be added. Tags are pointers to a commit, a snapshot. Tags aren't meant to change and don't move to another commit.

### HEAD-LESS or Detached HEAD State
- Occurs when checkout a commit or tag in a specific branch.
- Git moves HEAD pointer to the commit. 
- When you checkout a new branch, HEAD moves to that branch. The value of HEAD points to new SHA.
- There is not reference pointing to the commits you made in a detached state.
- If we want to save work in this state:
    - Create a new branch that points to the last commit you made in the detached state - git branch <new-branch-name> <commit>
    - Discard your work - dangling commit. If you don't do a new commit, these changes will be garbage collected.

## Merging and Rebasing

### Merging and Fast Forward
- Merge commits have more than one parent. 
- Fast forward happens there is a clear path from current branch to tip of target branch.
- To prevent fast forward - git merge --no-ff. This will force a merge commit.

### Merge Conflict
- Attempts to merge but files have diverged. Git stops until conflicts are resolved.
- Happens with branches with lots of contributors or branch with lots of changes.
- Git ReReRe - saves how you resolved a conflict and next conflict it will reuse the same solution. Useful for long lived feature branch (like a refactor) and rebasing.
- Two options to turn on ReReRe:
    1. git config rerere.enabled true
    2. use --global flag to enable for all projects

## History and Diffs
- Commit messages
- Git log
- Git show and diffs

### Useful Commit Messages
- Good commits preserve history of code base
- Helps with code reviews, debugging, creating release notes, rolling back, and associating code with issue or ticket tracker

### Git log
- Shows history of repository
- git log --since - to pass in time frame (e.g., git log --since="yesterday")
- git log --follow - log files that have been moved or renamed
- git log --grep <regexp> - search for git messages that match regular expression
- git log --diff-filter=R --stat - selectively includes and excludes files that have been added, deleted, modified, etc.
- can use ~ and ^ denoting parent and nth commits when searching

### Git show and diffs
- git show allows to look at commits
- git show <commit> - show commit and contents
- git show <commit> --stat - show changed files in commit
- git show <commit>:<file> - look at a file from another commit

- git diff shows changes between commits
- git diff - unstaged changes
- git diff --staged - staged changes
- diff branches - git branch --merged master - which branches merged with master
- git branch --no-merged master - which branches aren't merged with master

## Fixing mistakes 
- Checkut, clean, reset, and revert

### Git checkout
- Allows to restore working tree files or switch branches
- When checkout a branch the following happens:
    1. Change HEAD to point to the new branch
    2. Copy the commit snapshot to the staging area
    3. Update working area with branch contents
- Git checkout --file - replace the working area copy with the version from the current staging area. Note: this command will overwrite files in the working directory without warning.

### Git clean and reset
- Git clean - cleans working area by deleting untracked files. Note: this operation cannot be undone.
- Git clean --dry-run - shows what will be deleted
- Git clean -f - to do the deletion, git clean -d - to clean all files and directories

- Git reset - moves the HEAD and the branch reference, meaning the branch is now modified, whatever it was point to before is going to be different. If you pass file path, it's not going to move the pointer.
- Three options:
    1. Soft
    2. Mixed
    3. Hard

### Git revert
- Safe reset
- Creates new commit that specifies changes from original commit and does not change history

## Git rebase and amend
- Amend
- Rebase
- Fixup and autosquash
- Abort

### Git amend
- Quick and easy way shortcut that lets you make changes to the previous commit
- git commit --amend - will add files to last commit. SHA will update b/c of key/value pairing.

### Git rebase
- Pull latest changes from a branch (usually master) and apply our commits on top of them by changing the parent commit of our commits.
- Rebase - means to give a commit a new parent.
- Helps with merge conflicts. Makes history cleaner too.
- Commits can be edited, removed, combined, re-ordered, and inserted before they're replayed on top of HEAD.
- git rebase -i - interactive rebase that will open an editor with a list of todos.
- Can use rebase to split commits.

### Fixup and autosquash
- Amending an arbitrary commit
    1. git add new files
    2. git commit --fixup <SHA>
    3. git rebase -i --autosquash <SHA>^
    4. git will generate to dos, just save and quit
- git rebase --exec - will run command after every commit

### Abort
- git rebase --abort - cancels the rebase
- if going to rebase or change commit history, make a copy of the branch first - git branch my_branch_backup
- if rebase doesn't work, use git reset my_branch_backup --hard and delete the messed up branch that originally tried to rebase
- advantages of rebase - can slice and dice history, easy to fix previous mistakes in the code, can keep history neat and clean
