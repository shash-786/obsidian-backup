In eager instantiate the Logger was instantiated at the start of the program itself. But what if the instance should only get activated when required.

Lazy instantiate

```cpp
// logger.h
class Logger {
    std::string m_tag;
    FILE *m_pstream;
    inline static Logger* m_lptr;

    Logger();
   ~Logger();
    
    public:
    Logger(const Logger&) = delete;
    Logger& operator= (const Logger&) = delete;
};

// logger.cc
Logger& Logger::getInstanceLazily() {
    if (!Logger::m_lptr) {
        Logger::m_lptr = new Logger();
    }
    return *Logger::m_lptr;
}
```

The logger will only be instantiated when called by `getInstanceLazily()`. But is this leak safe? No. The destructor is never called here. 

Solution: 
1) Use smart pointers

```cpp
//logger.h
class Logger {
	...
	iniline static std::unique_ptr<Logger> m_lptr;
	Logger();
public
	~Logger();
	...
};

// logger.cc
Logger& Logger::getInstanceLazily() {
    if (!Logger::m_lptr) {
        Logger::m_lptr.reset(new Logger()); // <- use reset rather than make_unique since
		// std::make_unique is a global helper function which doesn't have access to the                 constructor.`std::make_unique` internally tries to call `new Logger()`
    }
    return *Logger::m_lptr;
}
```
Why do I need to make the destructor public because delete on `m_lptr` will try and invoke `~Logger()` which will be private, So it needs to be public, but then again we run into the risk of the instance being deleted by the user. Solution pass a `deleter` in the definition of the unique_ptr 

```cpp
//logger.h
class Logger {
	...
	struct Deleter {
		void operator()(Logger* p) {
			delete p;
		}
	};
	iniline static std::unique_ptr<Logger, Deleter> m_lptr;
	Logger();
	~Logger();
public
	...
};
```

## THREAD SAFETY
Our previous implementation were not thread safe. Here's a version using mutex which allow for thread safety.

```cpp
Logger& Logger::getInstanceLazily() {
    // SCENARIO 1 MAKE IT THREAD SAFE
    m_mut.lock();
    if (!Logger::m_lptr) {
		Logger::m_lptr = new Logger();
    }
    m_mut.unlock();
    return *Logger::m_lptr;
}
```

After the instance is created the locking mechanism serves as a bottleneck if the load if large. To fix this we can simply encapsulate the locking mechanism inside a check 

```cpp
Logger& Logger::getInstanceLazily() {
    if (!Logger::m_lptr) {
        m_mut.lock();
        if (!Logger::m_lptr) {
	        void* p = operator new (sizeof(Logger)); // <- raw memory allocated
	        Logger::m_lptr = static_cast<Logger*>(p); //  tells the compiler that you intend for this raw memory to eventually hold a Logger object. *** NOT INITIALIZED ***
	        new(p) Logger(); // ACTUAL INITIALIZATION
        }
        m_mut.unlock();
    }
    return *Logger::m_lptr;
}
```

BUT WHAT HAPPENS IF A THREAD 1 IS INITIALIZING `m_lptr` to logger and at this time time a THREAD 2 checks if the `m_lptr` is null or not since new is not an atomic operation data may be uninitialized and THREAD 2 will get REFERENCE OF A LOGGER INSTANCE which is not initialized <-- Holy shit

## MEYERS PATTERN
```cpp
Logger& Logger::getInstanceLazily() {
    // THREAD SAFE - MEYERS PATTERN
    // statics are thread safe since c++11
    static Logger logger;
    return logger;
}
```