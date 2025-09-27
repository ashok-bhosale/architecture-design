Design patterns in C# are categorized into three major groups:

1. **Creational Patterns** – Focus on object creation mechanisms.
2. **Structural Patterns** – Deal with object composition and relationships.
3. **Behavioral Patterns** – Focus on communication between objects.

---

## 1️⃣ **Creational Design Patterns** (Object Creation)

|Pattern|Description|
|---|---|
|**Singleton**|Ensures a class has only one instance and provides a global access point to it.|
|**Factory Method**|Defines an interface for creating objects but allows subclasses to alter the type of objects created.|
|**Abstract Factory**|Provides an interface for creating families of related objects without specifying their concrete classes.|
|**Builder**|Separates object construction from its representation, allowing the same construction process to create different representations.|
|**Prototype**|Creates new objects by copying an existing object, reducing the cost of new object creation.|

---

## 2️⃣ **Structural Design Patterns** (Object Composition)

|Pattern|Description|
|---|---|
|**Adapter**|Allows incompatible interfaces to work together by converting one interface into another.|
|**Bridge**|Decouples abstraction from implementation so that the two can evolve independently.|
|**Composite**|Composes objects into tree structures to represent part-whole hierarchies.|
|**Decorator**|Dynamically adds behavior to an object without modifying its structure.|
|**Facade**|Provides a simplified interface to a complex system of classes.|
|**Flyweight**|Reduces memory usage by sharing common object state instead of creating multiple instances.|
|**Proxy**|Provides a placeholder or surrogate for another object to control access to it.|

---

## 3️⃣ **Behavioral Design Patterns** (Object Communication)

|Pattern|Description|
|---|---|
|**Chain of Responsibility**|Passes requests along a chain of handlers, where each handler decides whether to process the request or pass it on.|
|**Command**|Encapsulates a request as an object, allowing parameterization of clients and request queuing.|
|**Interpreter**|Defines a grammar for a language and uses an interpreter to process sentences in the language.|
|**Iterator**|Provides a way to access elements of an aggregate object sequentially without exposing its underlying representation.|
|**Mediator**|Defines an object that centralizes communication between objects, reducing dependencies.|
|**Memento**|Captures and restores an object’s internal state without violating encapsulation.|
|**Observer (Pub-Sub)**|Defines a dependency between objects so that when one object changes state, all its dependents are notified.|
|**State**|Allows an object to change its behavior when its internal state changes.|
|**Strategy**|Defines a family of algorithms, encapsulates each one, and makes them interchangeable at runtime.|
|**Template Method**|Defines the skeleton of an algorithm, letting subclasses redefine specific steps.|
|**Visitor**|Allows new operations to be added to existing object structures without modifying them.|

---

Would you like any of these patterns explained with C# code examples? 🚀