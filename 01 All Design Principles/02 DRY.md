### **.NET Core Application with DRY (Don't Repeat Yourself) Principle**

The **DRY (Don't Repeat Yourself)** principle ensures that every piece of knowledge or logic in an application has a **single, authoritative representation** to avoid redundancy, make maintenance easier, and improve code readability.

---

## **📌 Scenario: E-Commerce Order Processing System**

### **Problem: Code Duplication in Order Processing**

Suppose we have an e-commerce application where order processing, payment, and notifications are handled in multiple places with **duplicate code**.

---

## **1️⃣ Example of Code Violation (Repeating Code)**

🚫 **Before: Without DRY (Repeated Code in Multiple Places)**

```csharp
public class OrderService
{
    public void PlaceOrder(Order order)
    {
        // Validate order
        if (order.Items.Count == 0)
        {
            throw new Exception("Order must have at least one item.");
        }

        // Save order to database
        using (var context = new AppDbContext())
        {
            context.Orders.Add(order);
            context.SaveChanges();
        }

        // Process Payment
        PaymentProcessor paymentProcessor = new PaymentProcessor();
        paymentProcessor.ProcessPayment(order);

        // Send email notification
        EmailService emailService = new EmailService();
        emailService.SendEmail(order.CustomerEmail, "Order Confirmation", "Your order has been placed.");
    }
}
```

🚨 **Problems:**

- **Database logic, validation, payment, and email notification are repeated** in multiple places.
- **Tight coupling** between classes makes it difficult to modify code.

---

## **2️⃣ Applying DRY Principle (Refactored Code)**

✅ **Solution: Extract common functionality into reusable services**

### **🔹 Step 1: Centralizing Database Operations in Repository Pattern**

```csharp
public interface IOrderRepository
{
    void AddOrder(Order order);
}

public class OrderRepository : IOrderRepository
{
    private readonly AppDbContext _context;

    public OrderRepository(AppDbContext context)
    {
        _context = context;
    }

    public void AddOrder(Order order)
    {
        _context.Orders.Add(order);
        _context.SaveChanges();
    }
}
```

---

### **🔹 Step 2: Centralizing Payment Processing**

```csharp
public interface IPaymentProcessor
{
    void ProcessPayment(Order order);
}

public class PaymentProcessor : IPaymentProcessor
{
    public void ProcessPayment(Order order)
    {
        // Payment processing logic
        Console.WriteLine("Payment processed successfully.");
    }
}
```

---

### **🔹 Step 3: Centralizing Email Notifications**

```csharp
public interface INotificationService
{
    void SendEmail(string to, string subject, string body);
}

public class EmailService : INotificationService
{
    public void SendEmail(string to, string subject, string body)
    {
        // Send email logic
        Console.WriteLine($"Email sent to {to}: {subject}");
    }
}
```

---

### **🔹 Step 4: Using Dependency Injection in OrderService**

✅ **Now, we inject dependencies instead of manually creating instances.**

```csharp
public class OrderService
{
    private readonly IOrderRepository _orderRepository;
    private readonly IPaymentProcessor _paymentProcessor;
    private readonly INotificationService _notificationService;

    public OrderService(
        IOrderRepository orderRepository,
        IPaymentProcessor paymentProcessor,
        INotificationService notificationService)
    {
        _orderRepository = orderRepository;
        _paymentProcessor = paymentProcessor;
        _notificationService = notificationService;
    }

    public void PlaceOrder(Order order)
    {
        // Validate order
        if (!ValidateOrder(order))
        {
            throw new Exception("Invalid order.");
        }

        // Save order
        _orderRepository.AddOrder(order);

        // Process payment
        _paymentProcessor.ProcessPayment(order);

        // Send email notification
        _notificationService.SendEmail(order.CustomerEmail, "Order Confirmation", "Your order has been placed.");
    }

    private bool ValidateOrder(Order order)
    {
        return order.Items.Count > 0;
    }
}
```

---

### **🔹 Step 5: Registering Dependencies in `Startup.cs`**

```csharp
services.AddScoped<IOrderRepository, OrderRepository>();
services.AddScoped<IPaymentProcessor, PaymentProcessor>();
services.AddScoped<INotificationService, EmailService>();
services.AddScoped<OrderService>();
```

---

## **🚀 Benefits of DRY Principle**

✅ **No Duplicate Code** – Every responsibility is handled in one place.  
✅ **Easier Maintenance** – If the email service logic changes, update it **once** in `EmailService`.  
✅ **Improved Readability** – Clear separation of concerns using dependency injection.  
✅ **Better Scalability** – Easily add new features without modifying multiple places.

---

## **Conclusion**

**By applying DRY, we have refactored the code to use reusable services, ensuring that logic is defined in a single place.** Would you like a **GitHub repo** with this example? 🚀