### **.NET Core Application with Law of Demeter (LoD)**

The **Law of Demeter (LoD)** is also known as the "**Principle of Least Knowledge**." It states that a module should **only interact with closely related components and should not access internal details of other objects**. This reduces dependencies, improves maintainability, and makes the system more modular.

---

## **📌 Scenario: Order Management System**

### **Problem: Too Many Indirect Dependencies**

Imagine an **Order Management System**, where an `OrderService` needs to fetch customer details before placing an order.

🚫 **Before: Violating Law of Demeter (Too Many Dependencies)**

```csharp
public class Customer
{
    public Address Address { get; set; }
}

public class Address
{
    public string City { get; set; }
}

public class OrderService
{
    public void PlaceOrder(Customer customer)
    {
        Console.WriteLine($"Order placed for customer in {customer.Address.City}");
    }
}
```

🚨 **Problems:**

- `OrderService` **directly accesses `Address` through `Customer`**, violating LoD.
- If the `Address` structure changes, **`OrderService` also breaks**, increasing maintenance issues.

---

## **2️⃣ Applying Law of Demeter (LoD)**

✅ **Solution: Restrict Direct Access and Introduce Encapsulation**

```csharp
public class Customer
{
    private Address _address;

    public Customer(Address address)
    {
        _address = address;
    }

    public string GetCity()
    {
        return _address.City; // Encapsulating access to Address
    }
}

public class OrderService
{
    public void PlaceOrder(Customer customer)
    {
        Console.WriteLine($"Order placed for customer in {customer.GetCity()}");
    }
}
```

🔹 `OrderService` **no longer directly accesses `Address`**.  
🔹 **Encapsulation ensures that changes in `Address` don’t break `OrderService`**.

---

## **🚀 Benefits of Law of Demeter (LoD)**

✅ **Better Encapsulation** – Limits **unnecessary dependencies**.  
✅ **More Maintainable Code** – Changes in internal details **don’t break other classes**.  
✅ **Improved Readability** – Fewer **nested object calls**.  
✅ **Easier Testing** – **Mocking dependencies is easier** because of limited interactions.

---

## **Conclusion**

By applying **LoD**, we improved the design by **reducing coupling and enhancing maintainability**. Would you like a **GitHub repo** with a working example? 🚀