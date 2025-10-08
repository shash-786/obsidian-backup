1. Index file (*Binary file*) or cache you could think of as a half commit.

2. So this piece of magic is where the commit is made from

3. The operations to modify the index file are `add` or `rm`

4. Index File is created only after the first `add` operation

##### What an Index file actually looks like ?

The index file has a signature for git to verify internally and a header which contains the version and the entries in the `directory_cache` or `index`

Each entry is then represented by  `cache_entry` which will have fields as shown below
```cpp
#define CACHE_SIGNATURE 0x44495243	/* "DIRC" */
struct cache_header {
	uint32_t hdr_signature ;
	uint32_t hdr_version ;
	uint32_t hdr_entries ;
} ;

struct cache_time {
	uint32_t sec ;
	uint32_t nsec ;
} ;

struct cache_entry {
	struct cache_time ce_ctime ; // <-- Created time
	struct cache_time ce_mtime ; // <-- modified time
	uint32_t ce_dev ; // <-- device number
	uint32_t ce_ino ; // <-- inode number
	uint32_t ce_mode ; // file mode (permissions and type). This stores if the file is a regular file, a symbolic link, or if it has the executable bit set.
	uint32_t ce_uid ; // <-- user id
	uint32_t ce_gid ; // <-- group id
	uint32_t ce_size ; // <-- File size in bytes (helps figure changed or not)
	uint8_t sha1[20] ; // <--sha-1 hash
	uint16_t ce_flags ; 
	std::string name ; // <-- relative path name
} ;

/*
CE_FLAGS

IMPORTANT
1. File Name Length (Lower 12 Bits)
Mask: CE_NAMEMASK (0x0fff or 4095).

Purpose: The lowest 12 bits store the length of the filename string stored in the name field of the cache_entry. This allows Git to quickly determine the size of the entry without relying on null termination in the index file itself.

2. Staging and Conflict Tracking (Bits 12 and 13)
The CE_STAGEMASK (0x3000) uses two bits to store the file's stage.

When a merge conflict occurs, Git uses these bits to hold three simultaneous versions of the file in the index: Stage 1 (Base), Stage 2 ("Our" version), and Stage 3 ("Their" version).

3. Operational and Boolean Flags (Bits 14 and 15)
CE_VALID (0x8000): This flag, when set, forces Git to assume the file is unchanged in the working directory. This is the core mechanism behind commands like git update-index --assume-unchanged.

CE_UPDATE (0x4000): This flag is used internally during complex operations (like merging) to mark an index entry that needs to be updated or deleted.
*/
```


