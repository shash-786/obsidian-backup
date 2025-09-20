`std::variant` represents a type-safe Union. An instance of `std::variant` at any given time either holds a value of one of its alternative types, or in the case of error - no value

Variant size is bigger than Union because it has type tag to track which of its possible types is currently active, which union does not.

```cpp
#include <iostream>
#include <string>
#include <variant>

int main (int argc, char** argv) {
	std::variant< int, float > data;
	data = 1;
	
	if (std::holds_alternative<int>(data)) {
		std::cout << "Found Integer " << std::get<int>(data) << std::endl;
	}
	
	data = 1.1f;
	if (std::holds_alternative<int>(data)) {
		std::cout << "Found Integer " << std::get<int>(data) << std::endl;
	} else if (const float* f = std::get_if<float>(&data)) {
		std::cout << "Found Floating point number " << *f << std::endl; 
	} else {
		std::cout << "Not Found Floating point number " << std::endl; 
	}
	return 0;
}

OUTPUT
Found Integer 1 
Found Floating point number 1.1
```