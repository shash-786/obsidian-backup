[Code](https://github.com/shash-786/Learning/tree/main/design-patterns/prototype)

The **Prototype** pattern delegates the cloning process to the actual objects that are being cloned. The pattern declares a common interface for all objects that support cloning. This interface lets you clone an object without coupling your code to the class of that object. Usually, such an interface contains just a single clone method.

The implementation of the clone method is very similar in all classes. The method creates an object of the current class and carries over all of the field values of the old object into the new one. You can even copy private fields because most programming languages let objects access private fields of other objects that belong to the same class.

An object that supports cloning is called a **prototype**. 

```cpp
#include <iostream>
#include <string>
#include <memory>

// ------------------------------------
// 1. Prototype (Abstract Base Class)
// ------------------------------------
class Vehicle {
public:
    virtual ~Vehicle() = default;

    // The Clone method: The contract that mandates self-copying.
    // It returns a pointer (here, a smart pointer) to a new Vehicle object.
    virtual std::unique_ptr<Vehicle> Clone() const = 0;

    // A simple method to demonstrate the unique identity of the cloned object
    virtual void drive() const = 0;
};

// ------------------------------------
// 2. Concrete Prototype (Car)
// ------------------------------------
class Car : public Vehicle {
private:
    std::string color_;
    int speed_;

public:
    // Constructor
    Car(std::string color, int speed) : color_(color), speed_(speed) {}

    // Copy Constructor (used implicitly by Clone())
    Car(const Car& other) : color_(other.color_), speed_(other.speed_) {
        std::cout << "  -- Car object copied (Color: " << color_ << ") --\n";
    }

    // Implementation of the Factory Method (the Clone operation)
    std::unique_ptr<Vehicle> Clone() const override {
        // The object calls its own copy constructor and wraps the result in a unique_ptr.
        // It knows its exact type is 'Car'.
        return std::make_unique<Car>(*this);
    }

    void drive() const override {
        std::cout << "Car is driving at " << speed_ << " mph.\n";
    }
};

// ------------------------------------
// 3. Client (The Code That Needs the Copy)
// ------------------------------------
void create_and_use_clone(const Vehicle& prototype) {
    // We only know this is a Vehicle, but we ask it to clone itself.
    std::unique_ptr<Vehicle> clone = prototype.Clone();

    // The client code is decoupled; it works regardless of whether the prototype
    // was a Car, Truck, or Bus.
    std::cout << "Original prototype object ID: " << &prototype << std::endl;
    std::cout << "Cloned object ID: " << clone.get() << std::endl;

    clone->drive();
}

int main() {
    // 1. Create the original (Prototype) object
    Car original_car("Red", 80);

    // 2. Client asks for a copy, only knowing the base interface (Vehicle).
    create_and_use_clone(original_car); 

    return 0;
}

```