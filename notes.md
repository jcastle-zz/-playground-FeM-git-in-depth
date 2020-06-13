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