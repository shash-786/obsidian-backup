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
Non owning pointer which does not increase the reference count
```cpp

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
}
```