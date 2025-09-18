
Class template
```cpp
#include <iostream>

template<typename T, int Size>
class Container {
	private:
	T* m_data;
	
	public:
	Container() {
		m_data = new T[Size];
	}
	
	~Container() {
		delete[] m_data;
	}
};

int main () {
	Container<int, 5> c1;
	Container<double, 3> c2;
}
```

Static Variables in Class Templating

```cpp
#include <iostream>

template<typename T, int Size>
class Container {
	private:
	T* m_data;
	
	public:
	Container() {
		m_data = new T[Size];
	}
	
	~Container() {
		delete[] m_data;
	}
	static T m_variable;
};

template<typename T, int Size>
T Container<T, Size>::m_variable;

int main () {
	Container<int, 5> c1;
	Container<double, 3> c2;
	
	// Container::m_variable = 7 --> Not allowed
	Container<int, 5>::m_variable = 7;
	Container<double, 3>::m_variable = 2;
}
```