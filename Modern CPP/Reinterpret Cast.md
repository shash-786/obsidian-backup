### Reinterpret Cast (Muy peligroso)

1. It is used when you want to work with bits.

```cpp
#include <iostream> 

struct myStruct {
	int x;
	int y;
	char c;
	bool b;
};

int main() {
	myStruct s;
	s.x = 5;
	s.y = 10;
	s.c = 'a';
	s.b = true;
	int * p = reinterpret_cast<int*>(&s);
	std::cout << *p << std::endl;
	p++;
	std::cout << *p << std::endl;
	p++ ;
	char* cptr = reinterpret_cast<char*>(p) ;
	std::cout << *cptr << std::endl;
	return 0;
}

// OUTPUT
5
10
a
```

2. It can perform dangerous conversions because it can typecast any pointer to any other pointer.
```cpp
#include <iostream>
using namespace std;

class Mango {
public:
	void eatMango() { cout << "eating Mango" << endl;
};


class Banana {
public:
	void eatBanana() { cout << "eating Banana" << endl;
};

int main() {
	Banana *b = new Banana();
	Mango *m = new Mango();
	Banana *newbanana = reinterpret_cast<Banana*>(m);
	newbanana->eatBanana(); // <-- Calls the function hardcoded ocated at the hardcoded address of Banana::eatBanana with argument of *this (which is the Mango Object)
	return 0;
}

// OUTPUT

```

## Why it is Undefined Behavior

This code is **Undefined Behavior (UB)** because you are attempting to treat a chunk of memory allocated for a `Mango` object as if it were a `Banana` object and then calling a member function on it.

- `reinterpret_cast` is the C++ tool for telling the compiler, "Trust me, I know what I'm doing, just treat this memory address as this new type."

- When you call `newbanana->eatBanana()`, the `this` pointer inside `eatBanana` points to the memory space of the `Mango` object. **The C++ standard offers no guarantee whatsoever** about what happens next. It could crash, print something else, or, as in your case, appear to work because of the simplicity of the function.


If these classes were more complex, contained data, or had virtual functions, this code would almost certainly crash (e.g., if it tried to access a member variable at an offset that only exists in `Banana`). **Never rely on `reinterpret_cast` to cast between unrelated object types.**