### Static Cast

1)  Used for performing Implicit conversion between types
2)  Improvement over c-style casting in the sense of Readability
3)  Use when conversion between types is provided through conversion operator or constructor
```cpp
#include <iostream>
#include <string>

class Int {
	int x;
	
	public: 
	Int (int _x) : x(_x) {
		std::cout << "conversion constructor" << std::endl ;
	}
	
	operator std::string() {
		std::cout << "conversion operator" << std::endl ;
		return std::to_string(x) ;
	}
};

int main () {
	Int obj(2) ;
	
	std::string str1 = obj ;
	obj = 20 ;
	
	std::string str2 = static_cast<std::string>(obj) ;
	obj = static_cast<Int> (30) ;
}


// OUTPUT
conversion constructor
conversion operator
conversion constructor
conversion operator
conversion constructor
```

5)  More restrictive than c-style casts
```cpp
#include <iostream>

int main () {
	char c ;
	int *p = (int*)(&c) ;
	*p  = 5 ;
	
	std::cout << "C-Style Cast works!" << std::endl; 
	
	int* ip = static_cast< int* > (&c) ;
}
```

6) `static_cast` avoid cast from derived to base pointer
```cpp
class Base {};
class Derived: private Base {};

int main() {
	Derived d1;
	Base *bp1 = (Base*)&d1;
	// Allowed at compile-time
	Base *bp2 = static_cast<Base*>(&d1); // Not allowed at compile-time
}
```

7) Use for all upcasts, but never use for confused down casts
```cpp
class Base {};
class Derived1: public Base {};
class Derived2: public Base {};

int main() {
	Derived1 d1;
	Derived2 d2;
	Base *bp1 = static_cast<Base*>(&d1);
	Base bp2 = static_cast<Base*>(&d2);
	Derived1 *d1p = static_cast<Derived1*>(bp2);
	Derived2 *d2p = static_cast<Derived2*>(bp1);
	return 0;
}
```
 
 8) static_cast is preferred when converting to `void*` OR from `void*`
 
```cpp
#include <iostream>

int main () {
	int i = 10 ;
	void *v = static_cast< void* >(&i) ;
	int *v = static_cast< int* >(&v) ;
}
```

