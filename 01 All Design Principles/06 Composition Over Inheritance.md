## **.NET Core Application with Composition Over Inheritance**

### **📌 Principle: Composition Over Inheritance**

- **Inheritance** creates a tightly coupled hierarchy, making changes difficult.
- **Composition** promotes flexibility by allowing objects to be assembled with different behaviors dynamically.
- **Prefer "Has-A" over "Is-A" relationships** to avoid deep class hierarchies.

---

## **🛠 Scenario: Logging in an Application**

### **Problem: Using Inheritance for Logging**

🚫 **Before: Violating Composition Over Inheritance (Tightly Coupled Code)**

```csharp
public class BaseLogger
{
    public void Log(string message)
    {
        Console.WriteLine($"Log: {message}");
    }
}

public class FileLogger : BaseLogger
{
    public void LogToFile(string message)
    {
        Log(message); // Uses BaseLogger
        Console.WriteLine($"Log written to file: {message}");
    }
}

public class OrderService : FileLogger
{
    public void ProcessOrder()
    {
        LogToFile("Processing order...");
    }
}
```

🚨 **Problems:**

- **OrderService is tightly coupled to FileLogger** → Can't easily change logging behavior.
- **Hard to extend** → If we need database logging, we must modify the inheritance tree.

---

## **✅ Solution: Using Composition (Flexible & Scalable Approach)**

Instead of inheritance, **inject logging behavior using an interface**.

```csharp
public interface ILogger
{
    void Log(string message);
}

public class ConsoleLogger : ILogger
{
    public void Log(string message)
    {
        Console.WriteLine($"Console Log: {message}");
    }
}

public class FileLogger : ILogger
{
    public void Log(string message)
    {
        Console.WriteLine($"Log written to file: {message}");
    }
}

// OrderService depends on ILogger, NOT a specific implementation
public class OrderService
{
    private readonly ILogger _logger;

    public OrderService(ILogger logger)
    {
        _logger = logger;
    }

    public void ProcessOrder()
    {
        _logger.Log("Processing order...");
    }
}
```

---

## **🎯 Benefits of Composition Over Inheritance**

✅ **Loosely Coupled Code** – `OrderService` can work with **any logging implementation**.  
✅ **Easier Maintenance** – New logging types (e.g., DatabaseLogger) can be **added without modifying existing classes**.  
✅ **More Flexibility** – **Switch logging behavior at runtime** without modifying `OrderService`.  
✅ **Better Testability** – **Mocking `ILogger`** makes unit testing easier.

---

## **🚀 Conclusion**

By applying **Composition Over Inheritance**, we made our system **flexible, testable, and easy to maintain**. Would you like a **GitHub repo** with a working example? 🚀