# Architectural Styles
Here’s a list of **Architectural Styles** commonly used in C#:

### **1. Layered Architecture (N-tier Architecture)**

A traditional architecture pattern where the application is divided into layers with specific responsibilities. Typically, these layers are:

- **Presentation Layer**: Handles user interaction (UI).
- **Business Logic Layer**: Contains the core business logic.
- **Data Access Layer**: Manages database interaction.
- **Database Layer**: Stores data (SQL, NoSQL).

**Usage**: It’s widely used in enterprise applications with clear separation of concerns, especially in ASP.NET MVC and ASP.NET Core applications.

### **2. Microservices Architecture**

Microservices divide an application into small, independently deployable services, each responsible for a specific business capability.

- **Independent Deployment**: Each microservice is independently deployable and can be scaled individually.
- **Decentralized Data**: Each microservice has its own data store.
- **Communication**: Microservices communicate over HTTP/REST, gRPC, or message brokers (e.g., RabbitMQ, Kafka).

**Usage**: Used for large-scale systems with multiple teams, scalability needs, and fault isolation.

### **3. Event-Driven Architecture (EDA)**

An architecture style where the system reacts to events.

- **Event Producers**: Trigger events.
- **Event Consumers**: Process events triggered by producers.
- **Event Broker**: Manages event distribution (e.g., using message queues like RabbitMQ, Kafka).

**Usage**: Common in systems requiring asynchronous communication and decoupling of components (e.g., order processing systems).

### **4. Client-Server Architecture**

The client-server model splits the system into two main components:

- **Client**: Requests data or services.
- **Server**: Provides resources or services to the client.

**Usage**: It’s a basic architecture used in most web applications where the client (browser) makes HTTP requests to the server (API).

### **5. Model-View-Controller (MVC)**

An architecture pattern that separates the application into three components:

- **Model**: Manages data and business logic.
- **View**: Displays data to the user.
- **Controller**: Handles user input, modifies the model, and updates the view.

**Usage**: Common in **ASP.NET Core MVC**, web applications, and APIs, providing a clean separation of concerns.

### **6. Onion Architecture**

Onion architecture emphasizes the separation of concerns by creating layers in a circular manner around the core business logic:

- **Core**: Contains business logic and domain models.
- **Domain Services**: Encapsulates domain logic and interacts with core entities.
- **Application Layer**: Provides application services and APIs for the user interface.
- **Infrastructure Layer**: Handles communication with external systems (databases, file systems).

**Usage**: Useful in complex applications where you need to decouple business logic and infrastructure code.

### **7. Command Query Responsibility Segregation (CQRS)**

CQRS splits the system into two models:

- **Command**: Manages write operations (commands) that modify the system's state.
- **Query**: Manages read operations (queries) that fetch data without modifying it.

**Usage**: Often used in systems with complex business logic or high-performance requirements, where read and write operations need to be optimized independently.

### **8. RESTful Architecture**

REST (Representational State Transfer) is an architectural style for distributed systems that use HTTP protocols. It relies on stateless communication and CRUD operations (Create, Read, Update, Delete).

**Usage**: Common in building APIs in **ASP.NET Core Web API** or **ASP.NET MVC**.

### **9. Service-Oriented Architecture (SOA)**

SOA is an architectural style where software components are distributed across multiple services, which communicate over a network.

- **Services**: Each service is self-contained, often with its own data.
- **Loose Coupling**: Services are loosely coupled but communicate through well-defined interfaces (often SOAP or REST).

**Usage**: Useful in large enterprises with various applications and systems that need to communicate with each other.

### **10. Pub/Sub (Publish-Subscribe) Architecture**

This style is based on the pub/sub messaging model, where publishers send messages to a central hub and subscribers receive those messages.

- **Publisher**: Sends events/messages.
- **Subscriber**: Listens for events/messages.
- **Broker**: Routes the messages to appropriate subscribers.

**Usage**: Often used in systems that require real-time communication or decoupled components (e.g., using **SignalR** in **ASP.NET Core** for real-time updates).

### **11. Hexagonal Architecture (Ports and Adapters)**

Hexagonal Architecture is designed to keep the core application logic (domain) independent from external systems, like databases or user interfaces.

- **Core Application**: Contains business logic.
- **Adapters**: Translate between the core application and external services.
- **Ports**: Define interfaces for external interaction.

**Usage**: Preferred when you want to create a flexible, testable application that is not dependent on specific external frameworks or technologies.

### **12. Layered MVC Architecture (MVVM)**

MVVM (Model-View-ViewModel) is an architectural pattern where the **ViewModel** acts as an intermediary between the **View** and **Model**.

- **Model**: Represents data and business logic.
- **View**: Represents the UI.
- **ViewModel**: Holds logic for the view’s presentation, making it easier to bind UI components.

**Usage**: Common in **WPF** and **Xamarin** applications.

---

### Summary:

The choice of **architectural style** depends on the application’s complexity, scalability needs, and the type of communication and components required. C# developers typically use these architectural patterns in **ASP.NET Core**, **Blazor**, **WPF**, and **Xamarin** applications. 

==================================

#  **Design Principles in C#**

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

### **4. YAGNI (You Ainâ€™t Gonna Need It)**

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

==================================

# Design patterns in C#

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
