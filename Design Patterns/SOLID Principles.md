1) Single Responsibility 
	* A class should have only one responsibility
	* Put each responsibility in a separate class

```cpp
class Notes {
public:
	void Add() {
	}
	void Remove() {
	}
	//void Display() {
	//	 THIS SHOULD NOT BE IN NOTES CLASS 
	//}
};

class View {
public:
	void Display(Notes* p_notes) {
		//DISPLAY IMPLEMENTATION
	}
} 
```

2) Open-closed Principle
	* Modules should be open for extension but closed for modification
	* Modification should be done by adding new code instead of modifying existing code

```cpp
class Area {
	 double calculate(Shape* s) {
		if (typeid(*s) == typeid(Circle))
			return 3.1415 * s -> radius * s -> radius;
		else if (typeid(*s) == typeid(Square))
			return s -> length * s -> length;
		// INSTEAD OF DOING multiple if/else 
	 }
};

// WE CAN DO THIS
class Circle : public Shape {
	public:
		double radius;
		double area() {
			this -> radius * this -> radius * 3.1415;
		}
};

class Area {
	 double calculate(Shape* s) {
		return s -> area();
		// INSTEAD OF DOING multiple if/else 
	 }
};
```

3) Liskov-Substitution Principle
	* Every subclass or derived class should be substitutable for their base or parent class.

```cpp
class Operation {
	virtual int ResultOf (int* begin, int* end) {
		return 0;
	}
};

class BoolOperation: public Operation {
	bool ResultOf (int* begin, int* end) override {
		return true;
	}
};

// NOW BOOL OPERATION IS NOT SUBSTITUTABLE FOR Operation
typedef ReturnType std::variant<int, bool>;
struct IOperation {
	virtual ReturnType ResultOf (int* begin, int* end) = 0;
	virtual ~IOperation() = default;
}; 

class Operation : public IOperation{
	virtual ReturnType ResultOf (int* begin, int* end) {
		return 0;
	}
};

class BoolOperation: public Operation {
	ReturnType ResultOf (int* begin, int* end) override {
		return true;
	}
};
```

4) Interface Segregation Principle
	* Clients should not be forced to depend on methods they don't use.
```cpp
struct IFile {
	virtual void Read() = 0;
	virtual void Write() = 0;
	virtual ~IFile() = default;
}

// USERS MAY NOT WANT TO USE BOTH
struct IFileReader {
	virtual void Read() = 0;
	virtual ~IFileReader() = default;
};

struct IFileWriter {
	virtual void Write() = 0;
	virtual ~IFileWriter() = default;
};
```

5) Dependency Inversion Principle
	* Your classes should depend upon interfaces or abstract classes instead of concrete classes and functions.