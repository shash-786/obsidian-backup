1. They are generally used as a global instance, why is that so bad? Because you hide the dependencies of your application in your code, instead of exposing them through the interfaces. Making something global to avoid passing it around is a [code smell](https://en.wikipedia.org/wiki/Code_smell).

```cpp
class Logger { /* ... singleton implementation ... */ }; // Global instance

class DataProcessor {
public:
	void processData() {
		// DataProcessor needs to log, but you don't see 'Logger' in its interface!
		Logger::getInstance().writeLog("Processing started");
		// ...
	}
};

// Usage:
DataProcessor processor;
processor.processData(); // You don't see any Logger being passed here
	```
  In this `DataProcessor` example, you don't see `Logger` in its constructor or `processData`'s parameters. The `DataProcessor` just _reaches out_ and grabs the global `Logger` instance. The dependency on `Logger` is **hidden** inside the `processData` method's implementation.

  #### Why is hiding dependencies bad?
- **Hard to Understand:** When you look at `DataProcessor`'s definition, you don't immediately know all the things it relies on. You have to read through its entire code to find all the hidden global calls. It's like a mystery.
    
- **Hard to Test:** This is the biggest problem.
    
    - If you want to test `DataProcessor::processData()` in isolation, you can't easily tell it to use a "fake" logger (a mock) that just records messages instead of writing to a file.
    - Since `DataProcessor` always uses the _real_ global `Logger`, your tests might actually write to a file, or one test's logging might interfere with another test. This makes tests unreliable.
    - **Less Flexible:** If you later decide you want `DataProcessor` to log to a different place (e.g., a database instead of a file), or you want different `DataProcessor` objects to log to different places, you're stuck. You can't easily "inject" a different logging mechanism because the dependency is hard-coded to the global `Logger::getInstance()`.

2. They violate the [single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle): by virtue of the fact that they control their own creation and life-cycle.


3. Tight Coupling & Difficulty in Faking
	Tight Coupling: This means your code becomes rigidly dependent on the specific, concrete implementation of the singleton. Instead of a class receiving its dependencies (like a Logger) through its constructor or method parameters (which allows for flexibility), it directly reaches out and calls `Logger::getInstance()`. This hard wires the connection.
	
	Difficulty in Faking/Mocking: Because of this tight coupling, you can't easily "fake out" or "mock" the singleton during unit tests. You can't tell your code, "For this test, use a special logger that just counts messages instead of writing to a file." The code is stuck using the real global instance. This makes it hard to isolate the code you're testing from the singleton's behavior.

4. Carrying State & Test Order Dependence 🚦
	Carrying State: A singleton exists for the entire lifetime of the application, and its internal data (its "state") persists. If a singleton's state changes during one part of the program (e.g., a Logger counts how many messages it has logged), that change remains for the rest of the program's execution.
	
	Hit to Testing (Order Dependence): This global, persistent state is a big problem for unit tests. Each unit test should be independent; it should run in a clean environment and its success or failure shouldn't depend on what happened in a previous test. If Test A modifies the singleton's state, Test B (which runs after Test A) might get unexpected results because it's starting with a "dirty" state, not a clean one. This forces you to run tests in a specific order, which is a major "no-no" for reliable and maintainable test suites.