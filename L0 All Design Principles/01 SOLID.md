Here’s a **.NET Core application** implementing **SOLID principles** with a **real-world example** of an **Order Processing System** for an e-commerce platform.

---

## **Project Structure**

```
OrderProcessingSystem/
│── BusinessLogic/
│   ├── Interfaces/
│   │   ├── IOrderProcessor.cs
│   │   ├── IPaymentProcessor.cs
│   │   ├── INotificationService.cs
│   ├── Models/
│   │   ├── Order.cs
│   │   ├── Customer.cs
│   ├── Services/
│   │   ├── OrderProcessor.cs
│   │   ├── PaymentProcessor.cs
│   │   ├── EmailNotificationService.cs
│── DataAccess/
│   ├── Repository/
│   │   ├── IOrderRepository.cs
│   │   ├── OrderRepository.cs
│── Presentation/
│   ├── Controllers/
│   │   ├── OrderController.cs
│── Program.cs
│── Startup.cs
```

---

### **1. Single Responsibility Principle (SRP)**

> **Each class should have one responsibility.**

✅ **Issue:** A single `OrderService` class handling order processing, payments, and notifications.  
✅ **Solution:** Split into multiple services.

#### **Before (Violation of SRP)**

```csharp
public class OrderService
{
    public void ProcessOrder(Order order)
    {
        // Process order logic
    }

    public void ProcessPayment(Order order)
    {
        // Payment processing logic
    }

    public void SendNotification(Order order)
    {
        // Send email notification
    }
}
```

🚫 **Problem:** This class handles too many responsibilities.

#### **After (Following SRP)**

```csharp
public class OrderProcessor : IOrderProcessor
{
    public void ProcessOrder(Order order)
    {
        // Order processing logic
    }
}

public class PaymentProcessor : IPaymentProcessor
{
    public void ProcessPayment(Order order)
    {
        // Payment processing logic
    }
}

public class EmailNotificationService : INotificationService
{
    public void SendNotification(Order order)
    {
        // Email sending logic
    }
}
```

✅ **Now, each class has a single responsibility!**

---

### **2. Open/Closed Principle (OCP)**

> **Classes should be open for extension but closed for modification.**

✅ **Issue:** If we add a new payment method, we must modify the `PaymentProcessor`.  
✅ **Solution:** Use **abstraction and extension**.

#### **Before (Violation of OCP)**

```csharp
public class PaymentProcessor
{
    public void ProcessPayment(Order order, string paymentType)
    {
        if (paymentType == "CreditCard")
        {
            // Process credit card
        }
        else if (paymentType == "PayPal")
        {
            // Process PayPal
        }
    }
}
```

🚫 **Problem:** We need to modify the class each time we add a new payment type.

#### **After (Following OCP)**

```csharp
public interface IPaymentProcessor
{
    void ProcessPayment(Order order);
}

public class CreditCardPaymentProcessor : IPaymentProcessor
{
    public void ProcessPayment(Order order)
    {
        // Process credit card payment
    }
}

public class PayPalPaymentProcessor : IPaymentProcessor
{
    public void ProcessPayment(Order order)
    {
        // Process PayPal payment
    }
}
```

✅ **Now, we can extend payment options without modifying existing code!**

---

### **3. Liskov Substitution Principle (LSP)**

> **Subclasses should be replaceable for their base class without breaking the system.**

✅ **Issue:** If a subclass overrides behavior incorrectly, it breaks functionality.  
✅ **Solution:** Use proper **inheritance** and **interfaces**.

#### **Before (Violation of LSP)**

```csharp
public class Discount
{
    public virtual decimal ApplyDiscount(decimal price)
    {
        return price - 10;
    }
}

public class NoDiscount : Discount
{
    public override decimal ApplyDiscount(decimal price)
    {
        throw new NotImplementedException();
    }
}
```

🚫 **Problem:** The `NoDiscount` class breaks functionality because it throws an exception.

#### **After (Following LSP)**

```csharp
public interface IDiscount
{
    decimal ApplyDiscount(decimal price);
}

public class FixedDiscount : IDiscount
{
    public decimal ApplyDiscount(decimal price) => price - 10;
}

public class NoDiscount : IDiscount
{
    public decimal ApplyDiscount(decimal price) => price;
}
```

✅ **Now, both implementations work correctly without breaking behavior!**

---

### **4. Interface Segregation Principle (ISP)**

> **A class should not be forced to implement methods it does not use.**

✅ **Issue:** A single large interface forces classes to implement unnecessary methods.  
✅ **Solution:** Use **smaller interfaces**.

#### **Before (Violation of ISP)**

```csharp
public interface IOrderService
{
    void ProcessOrder(Order order);
    void ProcessPayment(Order order);
    void SendNotification(Order order);
}
```

🚫 **Problem:** If a class only handles payments, it still needs to implement `ProcessOrder`.

#### **After (Following ISP)**

```csharp
public interface IOrderProcessor
{
    void ProcessOrder(Order order);
}

public interface IPaymentProcessor
{
    void ProcessPayment(Order order);
}

public interface INotificationService
{
    void SendNotification(Order order);
}
```

✅ **Now, classes implement only what they need!**

---

### **5. Dependency Inversion Principle (DIP)**

> **High-level modules should not depend on low-level modules. Both should depend on abstractions.**

✅ **Issue:** High-level classes directly create objects of low-level classes.  
✅ **Solution:** Use **dependency injection**.

#### **Before (Violation of DIP)**

```csharp
public class OrderService
{
    private readonly PaymentProcessor _paymentProcessor = new PaymentProcessor();

    public void ProcessOrder(Order order)
    {
        _paymentProcessor.ProcessPayment(order);
    }
}
```

🚫 **Problem:** `OrderService` is tightly coupled to `PaymentProcessor`.

#### **After (Following DIP)**

```csharp
public class OrderService
{
    private readonly IPaymentProcessor _paymentProcessor;

    public OrderService(IPaymentProcessor paymentProcessor)
    {
        _paymentProcessor = paymentProcessor;
    }

    public void ProcessOrder(Order order)
    {
        _paymentProcessor.ProcessPayment(order);
    }
}
```

✅ **Now, we can easily switch to different payment processors without modifying `OrderService`!**

---

## **Dependency Injection in .NET Core**

Add dependency injection in `Startup.cs`:

```csharp
services.AddScoped<IOrderProcessor, OrderProcessor>();
services.AddScoped<IPaymentProcessor, CreditCardPaymentProcessor>();
services.AddScoped<INotificationService, EmailNotificationService>();
```

---

## **Conclusion**

By applying **SOLID principles** in .NET Core:

- ✅ Code is **maintainable, scalable, and testable**.
- ✅ Components follow **separation of concerns**.
- ✅ System is **extensible** without modifying existing code.

Would you like a **GitHub repository** with this project? 🚀