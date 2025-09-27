### **.NET Core Application with Encapsulation**

### **📌 Principle: Encapsulation**

Encapsulation is one of the core concepts of Object-Oriented Programming (OOP) that emphasizes **bundling the data (variables) and methods that operate on the data into a single unit or class** and **restricting access** to some of the object’s components. This helps to protect the internal state of the object and allows controlled access through public methods (getters/setters).

---

## **🛠 Scenario: Bank Account System**

### **Problem: Exposing Internal Details Directly**

Suppose we are developing a **Bank Account System**. If we **expose internal data** such as account balance and allow direct modification, this can lead to **inconsistent states**.

🚫 **Before: Exposing Internal Data (Breaking Encapsulation)**

```csharp
public class BankAccount
{
    public string AccountHolder { get; set; }
    public decimal Balance { get; set; }

    public void Deposit(decimal amount)
    {
        Balance += amount;
    }

    public void Withdraw(decimal amount)
    {
        if (Balance >= amount)
            Balance -= amount;
        else
            Console.WriteLine("Insufficient funds.");
    }
}
```

🚨 **Problems:**

- **Balance is directly accessible and modifiable**, potentially leading to inconsistent states.
- **No control over the data**—users can set any value for `Balance`, even invalid values.

---

## **✅ Solution: Applying Encapsulation (Controlled Access)**

By **hiding the internal details** (private fields) and exposing controlled access via **methods**, we ensure the object's state is **consistent and secure**.

```csharp
public class BankAccount
{
    private decimal _balance;

    public string AccountHolder { get; set; }

    public decimal Balance
    {
        get { return _balance; }
        private set { _balance = value; }  // Make Balance private to modify internally only
    }

    public void Deposit(decimal amount)
    {
        if (amount > 0)
            _balance += amount;
        else
            Console.WriteLine("Deposit amount must be positive.");
    }

    public void Withdraw(decimal amount)
    {
        if (_balance >= amount)
            _balance -= amount;
        else
            Console.WriteLine("Insufficient funds.");
    }
}
```

🔹 **Private field `_balance`** prevents external modification.  
🔹 **Public methods** (`Deposit`, `Withdraw`) allow controlled access to the internal state.

---

## **🎯 Benefits of Encapsulation**

✅ **Data Protection** – Internal state is **protected** and can’t be modified directly.  
✅ **Control** – Access is **controlled** through methods, ensuring the integrity of the data.  
✅ **Easier Maintenance** – Changes to the internal implementation don't affect external code, as long as the interface remains the same.  
✅ **Improved Flexibility** – Future changes can be easily accommodated without breaking existing code.  
✅ **Cleaner Code** – Promotes **separation of concerns** by keeping the data and business logic in one class.

---

## **🚀 Conclusion**

By applying **Encapsulation**, we ensured that the **Bank Account** class maintains a **consistent and secure state**. The code is **easier to maintain**, and the internal workings are hidden from external users. Would you like a **GitHub repo** with a working example? 🚀