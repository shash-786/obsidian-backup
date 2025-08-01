[Singleton](https://gameprogrammingpatterns.com/singleton.html) Ensure that a class has only one instance and provide a global point of access to it.

Consider a class that wraps an underlying file system API. Because file operations can take a while to complete, our class performs operations asynchronously. This means multiple operations can be running concurrently, so they must be coordinated with each other. If we start one call to create a file and another one to delete that same file, our wrapper needs to be aware of both to make sure they don’t interfere with each other.

To do this, a call into our wrapper needs to have access to every previous operation. If users could freely create instances of our class, one instance would have no way of knowing about operations that other instances started. Enter the singleton.

[code-example](https://github.com/shash-786/Learning/tree/main/design-patterns/singleton)

```cpp
// Scenario 1 single instantiate
// class which interact with a common resource like filesystem need to be instantiated once to
// avoid unexpected behavior

//main.cc
void foo() {
    Logger _logger;
    _logger.write_log("Program Exec");
}

int main(int argc, char const *argv[]) {
    Logger _logger;
    _logger.write_log("Application Started");
    foo();
    _logger.write_log("Application Ended");
    return 0;
}

OUTPUT
[18:48:23] Program Exec 
|N|N|N|N|N       [18:48:39] Application Ended 

// logger.cc
Logger Logger::m_instance;

Logger& Logger::GetInstance() {
    return m_instance;
}

//main.cc
void foo() {
    Logger& _logger = Logger::GetInstance();
    _logger.write_log("Program Exec");
}

int main(int argc, char const *argv[]) {
    Logger& _logger = Logger::GetInstance(); // <-- need the referene symbol otherwise a copy is created and in foo the f_ptr is released resulting in double free and bad write in application ended
    _logger.write_log("Application Started");
    foo();
    _logger.write_log("Application Ended");
    return 0;
}


```
