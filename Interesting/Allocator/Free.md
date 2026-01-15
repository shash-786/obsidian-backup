### morecore (sbrk)

```c
// morecore: ask system for more memory
static Header*
morecore(unsigned nu)
{
	char *p;
	Header *hp;
	if(nu < NALLOC)
		nu = NALLOC;
		
	p = sbrk(nu * sizeof(Header));
	if(p == SBRK_ERROR)
		return 0;
		
	hp = (Header*)p;
	hp->s.size = nu;
	free((void*) (hp + 1)) ;
	return freep;
}
// no space at all
```

### Free

The CLL will be sorted in the order of increasing memory addresses. The for loop is basically for finding a spot where in the CHUNK previous to p will have a lower memory address and the CHUNK next to us will have higher.

Inside for loop the `if` block is for wrap around check. The `if` statement checks if the newly freed block (`bp`) belongs at either the extreme end or the extreme beginning of the list.

`bp > p` (The block belongs at the very end)
`bp < p->s.ptr` (The block belongs at the very beginning)

```c
void free (void *ap)
{
	Header *bp, *p;
	bp = (Header*)ap - 1; // point to block header
	for(p = freep; !(bp > p && bp < p->s.ptr); p = p->s.ptr)
		if(p >= p->s.ptr && (bp > p || bp < p->s.ptr))
			break; // freed block at start or end of arena
```


If the addresses of the block which we want to free aligns with a block before or after us we we attach the block with the respective block or we will just create the links.

```c
	if(bp + bp->s.size == p->s.ptr){ // join to upper neighbor
		bp->s.size += p->s.ptr->s.size;
		bp->s.ptr = p->s.ptr->s.ptr;
	} else {
		bp->s.ptr = p->s.ptr;
	}
	
	if(p + p->s.size == bp) {
		p->s.size += bp->s.size; // join to lower neighbor
		p->s.ptr = bp->s.ptr;
	} else {
		p->s.ptr = bp ;
	}
	freep = p ;
	return ;
}
```