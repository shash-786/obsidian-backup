Key Uses:

- **Callbacks:**
    
    Passing functions as arguments to other functions, enabling flexible and customizable behavior (e.g., sorting algorithms with different comparison functions).
    
- **Runtime Decision-Making:**
    
    Choosing which function to execute based on conditions at runtime.
    
- **Event Handling:**
    
    Implementing event-driven architectures where specific functions are called in response to events.
    
- **Implementing Polymorphism (in a C-style):**
    
    Achieving a form of dynamic dispatch by storing different function addresses in a structure.

```cpp

bool compare(const int& a, const int& b) {
    return a < b;
}

int main(int argc, char const *argv[]) {
    std::vector< int > nums = {2, 31, 3, -1, 9, -50};
    bool (*ptr_fn) (const int&, const int&);
    ptr_fn = compare;
    std::function<bool(const int&, const int&)> ptr_fn2;
    ptr_fn2 = compare;
    // std::sort(nums.begin(), nums.end(), compare); // <-- compare here is a function ptr
    // std::sort(nums.begin(), nums.end(), ptr_fn);
    std::sort(nums.begin(), nums.end(), ptr_fn2);
    for (int& num: nums) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    return 0;
}

```