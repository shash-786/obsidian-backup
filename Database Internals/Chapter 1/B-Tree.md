##### Disk structure
![[Disk_structure.png|266]]
Block address is given by (TrackNo, SectorNo)
Inside the Block (typically 512 bytes) addresses starts from 0 to 511. 

###### Index
Imagine a DB of employee data with each record of size 128 bytes. Meaning 4 of these records would fit in a block. Let's suppose there's 100's of records, so we would need (100/4) = 25 blocks. To find a record we would need to search at the most 25 blocks. To reduce this block access we have indexes.

Index is nothing but hash-map sort of thing where we have pointers to actual location on the disk for the record. If we have an index of *EID* --> *Pointer* each record is of 16 (8 + 8) bytes then to store this index in the disk and the final access of the pointer data we have cut down block access from 25 to ~4.

###### Multiple Indexes

Continuing from the previous explanation what if there were 1000+ rows in our DB. Then effectively just a single *EID*->*ptr* would not reduce the Block access by that much. We would need to search 33 blocks of 1000 *EID*->*ptr* records. 

In such cases we just add another level on top indirection wherein the top level Index would give us a smaller index and that smaller index would give us the final record. 

![[Multi-level-Index.png|372]]

###### M-way search Tree
In practice we want the indexes to be self managing, because manually adding index-levels is not efficient. So we look at B and B+ trees. 

Drawing from the concept of BST. In which we have 1 key and 2 children we have m-way search tree.

Here we can see in the root node we have **2 Keys - 6 and 17** meaning the leftmost child will be lower than the first key *6* and the rightmost child will be greater than *17*, and the middle child will be inbetween *6 and 17*
![[M-Way-Search-Tree.png]]
**If there are 'M' ways then there will be 'M - 1' Keys for a Node**

In practice insertion in M-way search tree follows no particular guideline. Meaning if we want to build a 10-way search tree from scratch and we have 10 keys ready we can build a skew tree removing any advantage of the M-way search tree all together.

These guidelines are what later change M-way search trees to B-trees. 

###### B-Tree

Guidelines -
	1. Every node must fill at least ceil (M / 2) children to prevent skew.
	2. Root can have minimum 2 children
	3. All leaf nodes must be at same level
	4. Creation approach is bottom-up (Tree grows up wards)

Insertion Process --> https://youtu.be/aZjYr87r1b8?t=1827


###### Node Structure of B-tree

Every Node contains **{Key} {Child Pointer} {Record Pointer}**. Every node you could think of as mini database. 

SQL databases are known to use B+ trees to hold data.
