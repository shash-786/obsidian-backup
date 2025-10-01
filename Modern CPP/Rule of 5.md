Upon the Rule of 3 two the 'Move constructor' and 'Move assignment operator' makes up the Rule of 5.
```cpp
// Move constructor
IntArray::IntArray(IntArray&& source){
	m name = source.m name;
	source.m name = "";
	m data = source.m data;
	source.m_data = nullptr;
	std::cout << m name << "was move constructed" << std::endl;
}

// Move assignment operator
IntArray& IntArray::operator=(IntArray&& source) {
	if(this!=&source){
		m name = source.m name;
		source.m name = "":
		m_data = source.m_data;
		source.m_data = nullptr;
	}
	std::cout << m name << " Used move assignment" << std::endl;
	return *this;
}

int main(int argc, char** argv) {
	std::vector< IntArray > myArrays;
	for (int i = 0; i < 5; i++) {
		IntArray temp(std::to_string(i));
		myArrays.push_back(temp); //<-- uses the Copy constructor
		myArrays.push_back(std::move(temp)); //<-- uses the Move constructor 
	}
}
/*
# std::vector<T,Allocator>::push_back

void push_back( const T& value );
void push_back( T&& value );
*/
```

