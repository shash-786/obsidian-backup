**Tight Coupling:**
Without a factory, the client code directly interacts with and depends on specific concrete classes (e.g., PizzaA, PizzaB). This tight coupling means changes to these concrete classes can break the client code, making the system less flexible.

**Complex Object Creation Logic:**
Systems that need to create different types of objects based on some input often end up with large, complex conditional statements (if-else or switch statements) within the client code to determine which object to instantiate. 

**Difficulty in Extending Functionality**
Adding new object types requires modifying the existing client code, which introduces risk and can break existing functionality. 