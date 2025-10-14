## Malloc


The Heap is Header-addressable. Header is a struct which contains the size of the chunk and the pointer to the next chunk. It is made a union because the address we get should be aligned so the `typdef Align long` is used in the union, because Long has strict alignment.

In the beginning we have a dummy **Header** `base` which will the start of the circular linked list, we will initialize it with size 0. `freep` will the pointer we will use to get the free nodes or chunks. 

```c
union header {
	struct {
		union header* ptr ;
		unsigned size ;
	} s ; 
	Align x ;
}

typedef long Align
typedef union header Header ;

// INITIALIZED TO NULL
static Header base ;
static Header* freep ;
```

Malloc starts with checking if `freep` is null which means the list does not exist. So we will simply create a list with one node 

base -> base
 ^
 |
freep
prevp


```c
void malloc(unisgned nbytes) {
	Header* p, *prevp ;
	unsigned nunits ;
	
	nunits = (nbytes + sizeof (Header) - 1)/sizeof(Header) + 1;
	if((prevp = freep) == 0) {
		base.s.ptr = freep = prevp = &base; // no free list yet
		base.s.size = 0;
	}	
```


Now we will do a simple traversal of the CLL (circular linked list) . At first iteration the `freep == prevp`, So we will need to allocate memory from `sbrk` and add it to the List [[Free]]

After that the process is simple, we will land onto the node and check if it's size is as per our requirement, if it is remove it from the list and return the address of the node(chunk) to the caller. 

But if the size is more we will need to decrement the size of the CHUNK by our requirement (if the size available is 10 we need to 4 we will subtract 4 from the current CHUNK) increment the `p` to point to the immediate position after the subtracted size and initialize the current positions size to our requirement. 

```c
	for(p = prevp->s.ptr; ; prevp = p, p = p->s.ptr) {
		if(p->s.size >= nunits){ // big enough
			if (p->s.size == nunits) {
				prevp->s.ptr = p->s.ptr ;
			} else {
				p->s.size -= nunits ;
				p += p->s.size ;
				p->s.size = nunits ; 
			}
			freep = prevp ;
			return (void *) p + 1 ; 
		}
		
		if (freep == prevp) {
			if ((p = morecore(nunits)) == 0) 
				return 0 ;
		}	
	}
}
```

