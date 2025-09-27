### **.NET Core Application with Favor Object Composition Over Static Methods**

### **📌 Principle: Favor Object Composition Over Static Methods**

The **"Favor Object Composition Over Static Methods"** principle emphasizes the use of **objects** and **dependency injection** rather than relying on **static methods** or **singletons**. Static methods often create tightly coupled code and make unit testing difficult. In contrast, object composition promotes **loose coupling** and **greater flexibility**.

---

## **🛠 Scenario: Payment Processing System**

Suppose you are building a **Payment Processing System** that calculates various discounts based on the type of payment method.

🚫 **Before: Using Static Methods (Tightly Coupled and Hard to Test)**

```csharp
public static class PaymentDiscountCalculator
{
    public static decimal CalculateDiscount(string paymentMethod)
    {
        if (paymentMethod == "CreditCard")
            return 0.1m; // 10% discount
        if (paymentMethod == "Paypal")
            return 0.05m; // 5% discount
        return 0;
    }
}

public class PaymentService
{
    public decimal ProcessPayment(string paymentMethod, decimal amount)
    {
        decimal discount = PaymentDiscountCalculator.CalculateDiscount(paymentMethod);
        decimal finalAmount = amount - (amount * discount);
        return finalAmount;
    }
}
```

🚨 **Problems:**

- **Tightly coupled** code, as `PaymentService` depends directly on the static `PaymentDiscountCalculator`.
- **Testing is difficult** because static methods cannot be easily mocked.
- **Hard to extend**: If new payment methods are added, modifications must be made to the static class and its usage in all code that depends on it.

---

## **✅ Solution: Favoring Object Composition (Loose Coupling, Easier to Test and Extend)**

By using **dependency injection** and **composition**, we can **inject** the necessary functionality into the `PaymentService` class, making it more flexible, testable, and extensible.

```csharp
public interface IDiscountCalculator
{
    decimal CalculateDiscount(string paymentMethod);
}

public class CreditCardDiscountCalculator : IDiscountCalculator
{
    public decimal CalculateDiscount(string paymentMethod)
    {
        return paymentMethod == "CreditCard" ? 0.1m : 0;
    }
}

public class PaypalDiscountCalculator : IDiscountCalculator
{
    public decimal CalculateDiscount(string paymentMethod)
    {
        return paymentMethod == "Paypal" ? 0.05m : 0;
    }
}

public class PaymentService
{
    private readonly IDiscountCalculator _discountCalculator;

    // Injecting the discount calculator through constructor (composition)
    public PaymentService(IDiscountCalculator discountCalculator)
    {
        _discountCalculator = discountCalculator;
    }

    public decimal ProcessPayment(string paymentMethod, decimal amount)
    {
        decimal discount = _discountCalculator.CalculateDiscount(paymentMethod);
        decimal finalAmount = amount - (amount * discount);
        return finalAmount;
    }
}
```

### **Usage Example with Dependency Injection**:

```csharp
// In Startup.cs (for .NET Core DI)
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IDiscountCalculator, CreditCardDiscountCalculator>();
    services.AddScoped<PaymentService>();
}
```

---

## **🎯 Benefits of Favoring Object Composition Over Static Methods**

✅ **Loose Coupling** – `PaymentService` does not depend on a static class but rather on an interface, allowing for **flexibility** in choosing the implementation.  
✅ **Easier Unit Testing** – Static methods are hard to mock, but with **composition**, dependencies are injected, making it easier to write unit tests.  
✅ **Extensibility** – New discount calculators can be added without modifying the core logic in `PaymentService`. You simply **add new implementations** of the `IDiscountCalculator` interface.  
✅ **Clearer Code** – The logic of payment discount calculation is encapsulated in separate, testable classes, making the code **more modular and maintainable**.  
✅ **Improved Flexibility** – The system can **switch implementations** of the discount calculator dynamically through dependency injection (DI), allowing easier updates and maintenance.

---

## **🚀 Conclusion**

By applying **Object Composition** over **Static Methods**, we made our `PaymentService` class **more flexible, testable**, and **extensible**. The design is **loose-coupled**, making it easier to maintain and extend. Would you like a **GitHub repo** with a working example? 🚀