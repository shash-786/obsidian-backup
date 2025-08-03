
**Lvalue** is an expression referring to an object where object is a region of storage. 
**Rvalue** Something that is not a lvalue

Most literals are rvalues like 
	numeric literals such as 3 or 3.14
	character literals such as 'a'
They don't necessarily occupy storage

However character string literals such as "xyzzy" are lvalues since they occupy data storage

*Lvalues used as rvalues*
An Lvalue can appear on either side of an assignment 
```cpp
int m, n;
m = n;
```
Here C++ performs lvalue-to-rvalue conversion.



**References**
A reference is essentially a pointer that's automatically de-referenced each time it's used

```cpp
int &ri = i;
//can be written as
int *const cpi = &i

ri = 4
//can be written as 
*cpi = 4;

int j = ri + 2;
//can be written as
int j = *cpi + 2;
```

### RValue
**Rvalue** Something that is not a lvalue
The 2 kinds of rvalues are

`xvalue` - which occupy storage
`prvalue` - which don't occupy any storage

```