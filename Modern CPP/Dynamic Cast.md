### Dynamic Cast

`dynamic_cast` is a type of cast operator used for safe down-casting and cross-casting within a polymorphic class hierarchy at runtime. It performs a runtime check to ensure the validity of the cast.

```cpp
// dynamic_cast in c++
// SYNTAX: dynamic_cast < new_type > ( expression)
// 1. dynamic_cast is used at run time to find out correct down-cast.
// NOTE0: Need at least one virtual function in base class.
// NOTE1: If the cast is successful, dynamic_cast returns a value of type new_type.
// NOTE2: If the cast fails and new_type is a pointer type, it returns a null pointer of that type.
// NOTE3: If the cast fails and new_type is a reference type, it throws an exception that matches a handler of type std::bad_cast.
#include <iostream>
using namespace std;
class Base {
	virtual void print() { cout << "Base" << endl; }
};

class Derived1: public Base {
	void print() { cout << "Derivedl" << endl; }
};

class Derived2: public Base {
	void print() { cout << "Derived2" << endl; }
};

int main() {
	Derived1 d1;
	Base *bp = dynamic_cast<Base*>(&d1);
	Derived2 *dp2 = dynamic_cast<Derived2*>(bp);
	return 0;
}
```


1. work only on polymorphic base class (at least one virtual function in base class) because it uses this information to decide about wrong down-cast.
2. it is used for up-cast (D->B) and down-cast (B->D), but it is mainly used for correct downcast.
3. Using this cast has run time overhead, because it checks object types at run time using RTTI (Run Time Type Information).
4. if we are sure that we will never cast to wrong object then we should always avoid this cast and use static_cast. 



Which C++ feature would you use to cast a pointer to class A into a derived class B?
> `dynamic_cast` 