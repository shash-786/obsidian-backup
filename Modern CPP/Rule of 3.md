The rule of three (also known as _the law of the big three_ or _the big three_) is a rule of thumb in C++ (prior to C++11 that claims that if a class defines any of the following then it should probably explicitly define all three

- destructor
- copy constructor
- copy assignment operator

```cpp
class Array {
public:
	Array() {
		this->data = new int[10];
		for (int i = 0;  i < 10; i++) {
			this->data[i] = i;	
		}
	}
	
	~Array() {
		delete[] this->data;
	}
	
	// COPY CONSTRUCTOR 
	// Array NewArray = ExistingArray;
	Array(const Array& rhs) {
		this->data = new int[10];
		for (int i = 0; i < 10; i++) {
			this->data[i] = rhs[i];
		}
	}
	
	// TO AVOID COPIES JUST DO
	Array(const Array& rhs) = delete;
	Array& operator=(const Array& rhs) = delete;
	
	// COPY ASSIGNMENT OPERATOR 
	// ExistingArray = OtherExistingArray;
	Array& operator=(const Array& rhs) {
		if (this->data = &rhs) {
			return *this->data;
		}
		
		delete[] this->data;
		this->data = new int[10];
		for (int i = 0; i < 10; i++) {
			this->data[i] = rhs[i];
		}
	}
private:
	int* data;
};
```

