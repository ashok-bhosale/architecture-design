I'll cover all design patterns with C# examples, starting with **Creational Patterns**, then **Structural Patterns**, and finally **Behavioral Patterns**.

---

## **1️⃣ Creational Design Patterns**

### **1. Singleton Pattern**

Ensures that a class has only **one instance** and provides a global point of access.

#### **C# Example:**

```csharp
public class Singleton
{
    private static Singleton _instance;
    private static readonly object _lock = new object();

    private Singleton() { } // Private constructor

    public static Singleton GetInstance()
    {
        if (_instance == null)
        {
            lock (_lock)  // Thread safety
            {
                if (_instance == null)
                    _instance = new Singleton();
            }
        }
        return _instance;
    }

    public void ShowMessage()
    {
        Console.WriteLine("Singleton instance accessed!");
    }
}

// Usage
class Program
{
    static void Main()
    {
        Singleton instance1 = Singleton.GetInstance();
        instance1.ShowMessage();
    }
}
```

---

### **2. Factory Method**

Defines an interface for creating objects but **lets subclasses decide** which class to instantiate.

#### **C# Example:**

```csharp
// Product interface
public interface IVehicle
{
    void Drive();
}

// Concrete Products
public class Car : IVehicle
{
    public void Drive() => Console.WriteLine("Driving a Car.");
}

public class Bike : IVehicle
{
    public void Drive() => Console.WriteLine("Riding a Bike.");
}

// Factory Class
public class VehicleFactory
{
    public static IVehicle GetVehicle(string type)
    {
        return type switch
        {
            "Car" => new Car(),
            "Bike" => new Bike(),
            _ => throw new ArgumentException("Invalid vehicle type")
        };
    }
}

// Usage
class Program
{
    static void Main()
    {
        IVehicle vehicle = VehicleFactory.GetVehicle("Car");
        vehicle.Drive();
    }
}
```

---

### **3. Abstract Factory**

Provides an interface for creating **families of related objects** without specifying their concrete classes.

#### **C# Example:**

```csharp
// Abstract Products
public interface IButton { void Render(); }
public interface ICheckbox { void Check(); }

// Concrete Products
public class WindowsButton : IButton
{
    public void Render() => Console.WriteLine("Windows Button Rendered.");
}

public class MacButton : IButton
{
    public void Render() => Console.WriteLine("Mac Button Rendered.");
}

// Abstract Factory
public interface IGUIFactory
{
    IButton CreateButton();
}

// Concrete Factories
public class WindowsFactory : IGUIFactory
{
    public IButton CreateButton() => new WindowsButton();
}

public class MacFactory : IGUIFactory
{
    public IButton CreateButton() => new MacButton();
}

// Usage
class Program
{
    static void Main()
    {
        IGUIFactory factory = new WindowsFactory();
        IButton button = factory.CreateButton();
        button.Render();
    }
}
```

---

### **4. Builder Pattern**

Separates **object construction** from its representation.

#### **C# Example:**

```csharp
// Product
public class Computer
{
    public string CPU { get; set; }
    public string RAM { get; set; }
    public string Storage { get; set; }

    public void ShowConfig() =>
        Console.WriteLine($"CPU: {CPU}, RAM: {RAM}, Storage: {Storage}");
}

// Builder Interface
public interface IComputerBuilder
{
    void SetCPU();
    void SetRAM();
    void SetStorage();
    Computer GetComputer();
}

// Concrete Builder
public class GamingComputerBuilder : IComputerBuilder
{
    private Computer _computer = new Computer();

    public void SetCPU() => _computer.CPU = "Intel i9";
    public void SetRAM() => _computer.RAM = "32GB";
    public void SetStorage() => _computer.Storage = "1TB SSD";

    public Computer GetComputer() => _computer;
}

// Director
public class Director
{
    public Computer Construct(IComputerBuilder builder)
    {
        builder.SetCPU();
        builder.SetRAM();
        builder.SetStorage();
        return builder.GetComputer();
    }
}

// Usage
class Program
{
    static void Main()
    {
        Director director = new Director();
        IComputerBuilder builder = new GamingComputerBuilder();
        Computer computer = director.Construct(builder);
        computer.ShowConfig();
    }
}
```

---

### **5. Prototype Pattern**

Creates a new object by **copying an existing object** instead of creating one from scratch.

#### **C# Example:**

```csharp
// Prototype Interface
public interface IPrototype
{
    IPrototype Clone();
}

// Concrete Prototype
public class Employee : IPrototype
{
    public string Name { get; set; }
    public string Department { get; set; }

    public IPrototype Clone() => (IPrototype)this.MemberwiseClone();

    public void ShowDetails() => Console.WriteLine($"Name: {Name}, Dept: {Department}");
}

// Usage
class Program
{
    static void Main()
    {
        Employee emp1 = new Employee { Name = "John", Department = "IT" };
        Employee emp2 = (Employee)emp1.Clone();
        emp2.Name = "Mike"; // Changing cloned object's name

        emp1.ShowDetails();
        emp2.ShowDetails();
    }
}
```

---

## ✅ **Next Steps**

I’ll continue with **Structural Patterns** next. Would you like me to proceed, or do you want explanations for any of these patterns before moving forward? 🚀