# Notes

## Commands
- Provide sha1 of current blob - echo 'Hello World' | git hash-object --stdin

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