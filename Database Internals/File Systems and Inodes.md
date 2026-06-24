
File storage in Unix systems happen on the basis of block size. The inodes simply point to these blocks with bigger files having indirections from inodes to inodes.

If there are going to be multiple small files we can reduce the block size to reduce wastage and internal fragmentation.

Large block sizes are really good for sequential reads because of eager caching the later blocks which are just next in memory.

Small block sizes lead to performance degradation because the number of references to blocks and pointer indirection increases.


##### Inodes
Just store metadata about files and directories and also contain other inodes
Directory structures also store filenames and their corresponding inode numbers

##### Allocating space for files
Can be done by using the next block in disk, or creating a free list of blocks, or creating an index of free blocks.


##### Readdirplus vs Readdir
Consider if a NFS client is making a readdirplus call which returns the metadata about each file inside a directory. The Readdirplus would have to read the contents of the inode itself leading to significant slowness in Ls calls compared to plain Readdir. Moreover with directories having more than 100k entries per directory performance starts going down because all of these entries cannot be stored in one page cache and needs multiple pages to be stored.

