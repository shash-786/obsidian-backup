1) Unique Pointer
```cpp
// CANNOT BE SHARED
// SCOPED POINTERS
// RESOURCE BOUND TO THE PTR WILL BE DELETED ONCE OUT OF SCOPE

int main () { 
	std::unique_ptr< Logger > l = std::make_unique< Logger >(params);
	auto copy = l; // <-- not allowed
	auto correct_copy = std::move(l);
}
```

2) Shared Pointer
`std::shared_ptr` is a smart pointer that retains shared ownership of an object through a pointer. Several `shared_ptr` objects may own the same object.
*Use count is thread safe*
```cpp
/*
 The object is destroyed and its memory deallocated when either of the following happens:

- the last remaining `shared_ptr` owning the object is destroyed;
- the last remaining `shared_ptr` owning the object is assigned another pointer via [operator=] or reset()

*/

int main(int argc, char* argv) {
	{
		std::shared_ptr< Logger > p1 = std::make_shared< Logger >(params);
		{
			std::shared_ptr< Logger > p2 = p1;
			std::cout << p2.use_count() << std::endl; // <-- 2
		}
		std::cout << p1.use_count() << std::endl; // <-- 1 
	} // <-- Object deleted here 
	
	std::cout << "Logger Destructor will be invoked before this line" << std::endl;

    {
        std::shared_ptr<Foo> sptr1 = std::make_shared<Foo>(300);
        std::shared_ptr<Foo> sptr2 = sptr1;
        std::shared_ptr<Foo> sptr3 = sptr2;
        std::cout << "Foo::bar = " << sptr1->getBar() << ", use_count() = "
                  << sptr1.use_count() << '\n'; // <-- 3 here
                  
        // Reset the shared_ptr sptr1, hand it a fresh instance of Foo.
        // The old instance will stay shared between sptr2 and sptr3.
        std::cout << "call sptr1.reset()...\n";
        sptr1.reset(new Foo{333});
       // NOTE THAT THE OLD INSTANCE IS NOT DELETED AFTER `sptr1` is deleted since
       // TWO OTHER pointers still point to it
	}
}
```

3) Weak Pointer
Non owning pointer which does not increase the reference count. `std::weak_ptr` models temporary ownership: when an object needs to be accessed only if it exists, and it may be deleted at any time by someone else, `std::weak_ptr` is used to track the object, and it is converted to `std::shared_ptr` to acquire temporary ownership.

```cpp
#include <iostream>
#include <memory>
 
std::weak_ptr<int> gw;
 
void observe()
{
    std::cout << "(before shared_ptr convert) gw.use_count() == " << gw.use_count() << ";\n"; 
    // we have to make a copy of shared pointer before usage:
    if (std::shared_ptr<int> spt = gw.lock()) {
        std::cout << "(after shared_ptr convert) gw.use_count() == " << gw.use_count() << ";\n";
        std::cout << "*spt == " << *spt << '\n';
    }
    else
        std::cout << "gw is expired\n";
}
 
int main()
{
    {
        auto sp = std::make_shared<int>(42);
        gw = sp;
        observe();
    } // <- shared pointer is deleted as it goes out of scope
    observe();
}
/*
	(before shared_ptr convert) gw.use_count() == 1;
	(after shared_ptr convert) gw.use_count() == 2;
	*spt == 42
	(before shared_ptr convert) gw.use_count() == 0;
	gw is expired
*/
```

Cyclic References
*The **underlying object** (or more accurately, a **control block** associated with the underlying object) holds the reference count*
```cpp

#include <iostream>
#include <memory>
#include <utility>

struct A;
struct B;

struct A {
    std::shared_ptr< B > b_ptr;
    A () {
    }
    
    void setB(const std::shared_ptr< B > temp) {
        this->b_ptr = temp;
    }
    
    ~A () {
        std::cout << "A destructor called" << std::endl;
    }
};

struct B {
    std::shared_ptr< A > a_ptr;
    B (const std::shared_ptr< A > temp) {
        this->a_ptr = temp;
    }
    
    ~B () {
        std::cout << "B destructor called" << std::endl;
    }
};

int main() {
    
    std::shared_ptr< A > ptr1 = std::make_shared< A >();
    std::shared_ptr< B > ptr2 = std::make_shared< B >(a);
    a->setB(b);
    return 0;
} // ptr1 and ptr2 will go out of scope here but 
// <-- here obj a and b still have ref count of 1 which was never decremented
```

![[Pasted image 20250805222407.png]]
```cpp
#include <iostream>
#include <memory>
#include <utility>

struct A;
struct B;

struct A {
    std::shared_ptr< B > b_ptr;
    A () {
    }
    
    void setB(const std::shared_ptr< B > temp) {
        this->b_ptr = temp;
    }
    
    ~A () {
        std::cout << "A destructor called" << std::endl;
    }
};

struct B {
    std::weak_ptr< A > a_ptr;
    B (const std::shared_ptr< A > temp) {
        this->a_ptr = temp;
    }
    
    ~B () {
        std::cout << "B destructor called" << std::endl;
    }
};

int main() {
    
    std::shared_ptr< A > ptr1 = std::make_shared< A >();
    std::shared_ptr< B > ptr2 = std::make_shared< B >(ptr1);
    ptr1->setB(ptr2);
    return 0;
}
/*
NOW BOTH THE DESTRUCTORS WILL BE CALLED THE `a_ptr` inside object pointed to by ptr2, points to ptr1 but doesn't increase it's ref count, so when the main returns ObjA will have a ref_count of zero and thus will be destroyed
and hence in std::shared_ptr::~b_ptr will also be called ObjB ref_count will also become 0 and be destroyed
*/
```

