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

##### B-Tree

Guidelines -
	1. Every node must fill at least ceil (M / 2) children to prevent skew.
	2. Root can have minimum 2 children
	3. Min keys for a Node is `2 * ([M / 2] - 1) ` (except root it can have 1 as well)
	4. All leaf nodes must be at same level
	5. Creation approach is bottom-up (Tree grows up wards)

Insertion Process --> https://youtu.be/aZjYr87r1b8?t=1827

###### Node Structure of B-tree

Every Node contains **{Key} {Child Pointer} {Record Pointer}**. Every node you could think of as mini database. 

###### Why this might not be efficient? 
In database one node is typically a page size (4KB or 16KB). Let’s assume:
a) Key = 8 bytes
b) Child pointer = 8 bytes
c) Record pointer = 8 bytes
Total per entry = 24 bytes

So in a 4KB page we can have at most (*4096/24*) 170 entries. which is not bad but will lead to a lower fanout (Less children so possibly more height) resulting in longer trees. 

Also imagine a range query `SELECT * FROM TABLE WHERE KEY BETWEEN 1 AND 40`
In a B-tree the keys are scattered across the tree and pointing to each key in the range would result in redundant traversals (multiple back-and-forth traversals) . This problem is solved by B+ trees.


##### B+ Tree

B+ tree was designed to tackle some of the inefficiencies of the B Tree in terms of I/O and queries. 

1) In B+ Tree the node structure of every node (except the leaf) changed from 
	**{Key} {Child Pointer} {Record Pointer}** --> **{Key }{Child Pointer}**

	These internal nodes are used purely for **navigation**.
	- Removing record pointers reduces the size of each entry
	- This allows **more keys per node**
	- Resulting in:
	    - Higher **fanout**
	    - Smaller **tree height**
	    - Fewer **disk I/O operations**

2) Secondly to tackle the range queries effectively, the leaf nodes were made into a linked list. 
	Each Leaf node contained a group of actual records.

	So if hypothetically we were not using a B or B+ tree for storing data and using a linear file to store everything we would have an average TC of O(N) for all operations. 

	With B+ tree, if given a particular key we tweak the TC down to O(LogN) in terms of searching the key alone, and then based if asked for a range query we can have the benefit of the linear layout and just get the range.


SQL databases are known to use B+ trees to hold data.
