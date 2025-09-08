

Define an interface for creating an object but let sub-classes decide which class to instantiate. Factory method lets the class defer instantiate to sub-classes.

An overridable method is provided that returns instance of a class. This method can be overridden to return instance of a sub-class. Behaves like a constructor.


```cpp
class vehicle {
	public:
	    virtual void printVehicle() = 0; // Abstract method
};

class car : public vehicle {
	public:
		void printVehicle() override {
			std::cout << "car" << std::endl;
		}
};

class bike : public vehicle {
	public: 
	void printVehicle() override {
		std::cout << "bike" << std::endl;
	}
};


class Factory {
	public:
	virtual vehicle* create()= 0 ;
};

class car_factory: public Factory {
	public:
	vehicle* create() override {
		return new car();
	}
};

class bike_factory: public Factory {
	public:
	vehicle* create() override {
		return new bike();
	}
};
```

Without the factory method the 
