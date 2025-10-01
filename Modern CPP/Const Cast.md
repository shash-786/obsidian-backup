### Const Cast

1) The expression `const_cast< T >(v)` can be used to change the const and volatile qualifiers of pointers and references, Where T must be a pointer, reference, or a pointer-to-member type.

```cpp
#include <iostream>
 
struct type
{
    int i;
 
    type(): i(3) {}
 
    void f(int v) const
    {
        // this->i = v;                 // compile error: this is a pointer to const
        const_cast<type*>(this)->i = v; // OK as long as the type object isn't const
    }
};
 
int main()
{
    // WHEN ACTUAL REFERRED OBJECT IS NOT CONST
    int i = 3;                 // i is not declared const
    const int& rci = i;
    const_cast<int&>(rci) = 4; // OK: modifies i
    std::cout << "i = " << i << '\n';
    
    const int* ptr = &i ;
    int* ptr2 = const_cast< int* >(ptr) ;
    *ptr2 = 5 ;
    std::cout << "i = " << i << std::endl ;
    
    type t; // if this was const type t, then t.f(4) would be undefined behavior
    t.f(4);
    std::cout << "type::i = " << t.i << '\n'; 
    
    
    // WHEN REFERRED OBJECT IS CONST LEADS TO UNDEFINED BEHAVIOR
    const int j = 3; // j is declared const
    [[maybe_unused]]
    int* pj = const_cast<int*>(&j);
    // *pj = 4;      // undefined behavior
    
    [[maybe_unused]]
    void (type::* pmf)(int) const = &type::f; // pointer to member function
    // const_cast<void(type::*)(int)>(pmf);   // compile error: const_cast does
                                              // not work on function pointers
}

// OUTPUT
i = 4
i = 5
type::i = 4
```

2) When we need to call some 3'rd party library where it is taking variable/object as non-const but not changing that.

```cpp
#include <iostream> 

void thirdPartyLibrary(int* x) {
	int k = 10;
	cout << k+*(x);
}

int main() {
	const int x = 20;
	const int *px = &x;
	thirdPartyLibrary(const_cast<int*>(px));
}
```
