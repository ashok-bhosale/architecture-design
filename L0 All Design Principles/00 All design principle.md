### **List of Design Principles in C#**

Design principles are guidelines that help developers write clean, maintainable, and scalable code. Below are the essential design principles in C#:

---

### **1. SOLID Principles** (Most Important)

**SOLID** is an acronym for five design principles that improve software maintainability and flexibility.

1. **Single Responsibility Principle (SRP)**
    
    - A class should have only one reason to change.
    - Example: A `Logger` class should handle logging, not file writing.
2. **Open/Closed Principle (OCP)**
    
    - A class should be open for extension but closed for modification.
    - Example: Use inheritance or strategy pattern instead of modifying existing code.
3. **Liskov Substitution Principle (LSP)**
    
    - Derived classes should be replaceable with their base classes without breaking functionality.
    - Example: A `Square` should not inherit from a `Rectangle` if it changes behavior.
4. **Interface Segregation Principle (ISP)**
    
    - A class should not be forced to implement interfaces it does not use.
    - Example: Instead of one large `IWorker` interface, create `IWorkable`, `IEatable`, etc.
5. **Dependency Inversion Principle (DIP)**
    
    - High-level modules should not depend on low-level modules. Both should depend on abstractions.
    - Example: Use dependency injection with interfaces rather than creating objects inside a class.

---

### **2. DRY (Don't Repeat Yourself)**

- Avoid code duplication by reusing functions, classes, or modules.
- Example: Move repeated code into a helper method or base class.

---

### **3. KISS (Keep It Simple, Stupid)**

- Write simple and readable code, avoiding unnecessary complexity.
- Example: Avoid deeply nested loops; use LINQ or methods for readability.

---

### **4. YAGNI (You Ain’t Gonna Need It)**

- Do not add functionality unless absolutely necessary.
- Example: Don't write methods for future needs that are not currently required.

---

### **5. Law of Demeter (LoD)**

- A class should only interact with its immediate dependencies.
- Example: Avoid calling methods of objects returned by another method (`obj1.GetObj2().GetValue()`).

---

### **6. Composition Over Inheritance**

- Prefer composition (using interfaces and dependency injection) over deep inheritance trees.
- Example: Instead of inheriting behavior, inject dependencies through constructors.

---

### **7. Encapsulation**

- Hide implementation details and expose only what is necessary.
- Example: Use private fields with public getters/setters.

---

### **8. Favor Object Composition Over Static Methods**

- Use dependency injection instead of static helper methods.
- Example: Inject a `Logger` class instead of calling `Logger.Log()` statically.

---

### **9. Principle of Least Astonishment (POLA)**

- Code should behave in a way that does not surprise developers.
- Example: Naming methods based on common expectations (`GetById(int id)` should return a record).

---

### **10. Fail Fast**

- Validate input early and throw errors as soon as possible.
- Example: Use guard clauses to check for `null` at the start of a method.

---

### **11. Persistence Ignorance**

- Business logic should not depend on database or framework details.
- Example: Keep domain models independent of Entity Framework.

---

### **12. Separation of Concerns (SoC)**

- Separate different functionalities into different modules or layers.
- Example: Follow MVC or layered architecture.

---

Would you like an example demonstrating these principles in a real project? 🚀