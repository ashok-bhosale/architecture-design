Great! Now, let's move on to **Structural Design Patterns** in C#.

---

## **2️⃣ Structural Design Patterns** (Object Composition)

### **1. Adapter Pattern**

The **Adapter Pattern** allows incompatible interfaces to work together by converting one interface into another.

#### **C# Example:**

```csharp
// Target Interface
public interface ITarget
{
    void Request();
}

// Adaptee (Existing Class)
public class Adaptee
{
    public void SpecificRequest() => Console.WriteLine("Adaptee SpecificRequest called.");
}

// Adapter (Adapts Adaptee to Target)
public class Adapter : ITarget
{
    private readonly Adaptee _adaptee = new Adaptee();

    public void Request() => _adaptee.SpecificRequest();
}

// Usage
class Program
{
    static void Main()
    {
        ITarget target = new Adapter();
        target.Request(); // Calls Adaptee's SpecificRequest
    }
}
```

---

### **2. Bridge Pattern**

The **Bridge Pattern** separates an abstraction from its implementation, allowing them to evolve independently.

#### **C# Example:**

```csharp
// Implementation
public interface IRenderer
{
    void Render(string shape);
}

public class VectorRenderer : IRenderer
{
    public void Render(string shape) => Console.WriteLine($"Drawing {shape} as vector.");
}

public class RasterRenderer : IRenderer
{
    public void Render(string shape) => Console.WriteLine($"Drawing {shape} as pixels.");
}

// Abstraction
public abstract class Shape
{
    protected IRenderer _renderer;

    protected Shape(IRenderer renderer) => _renderer = renderer;

    public abstract void Draw();
}

// Refined Abstraction
public class Circle : Shape
{
    public Circle(IRenderer renderer) : base(renderer) { }

    public override void Draw() => _renderer.Render("Circle");
}

// Usage
class Program
{
    static void Main()
    {
        Shape shape = new Circle(new VectorRenderer());
        shape.Draw(); // Drawing Circle as vector.
    }
}
```

---

### **3. Composite Pattern**

The **Composite Pattern** is used to build a tree structure of objects, where individual and composite objects are treated uniformly.

#### **C# Example:**

```csharp
// Component
public abstract class Graphic
{
    public abstract void Draw();
}

// Leaf
public class Circle : Graphic
{
    public override void Draw() => Console.WriteLine("Drawing Circle.");
}

// Composite
public class Group : Graphic
{
    private readonly List<Graphic> _graphics = new List<Graphic>();

    public void Add(Graphic graphic) => _graphics.Add(graphic);

    public override void Draw()
    {
        foreach (var graphic in _graphics)
            graphic.Draw();
    }
}

// Usage
class Program
{
    static void Main()
    {
        var circle1 = new Circle();
        var circle2 = new Circle();
        var group = new Group();
        group.Add(circle1);
        group.Add(circle2);

        group.Draw(); // Drawing Circle, Drawing Circle
    }
}
```

---

### **4. Decorator Pattern**

The **Decorator Pattern** dynamically extends the functionality of an object without modifying its structure.

#### **C# Example:**

```csharp
// Component
public interface INotifier
{
    void Send(string message);
}

// Concrete Component
public class EmailNotifier : INotifier
{
    public void Send(string message) => Console.WriteLine($"Email: {message}");
}

// Decorator
public class SMSNotifier : INotifier
{
    private readonly INotifier _notifier;

    public SMSNotifier(INotifier notifier) => _notifier = notifier;

    public void Send(string message)
    {
        _notifier.Send(message);
        Console.WriteLine($"SMS: {message}");
    }
}

// Usage
class Program
{
    static void Main()
    {
        INotifier notifier = new SMSNotifier(new EmailNotifier());
        notifier.Send("Hello, World!");
    }
}
```

---

### **5. Facade Pattern**

The **Facade Pattern** provides a simplified interface to a complex system.

#### **C# Example:**

```csharp
// Subsystems
public class SubsystemA
{
    public void OperationA() => Console.WriteLine("Subsystem A operation");
}

public class SubsystemB
{
    public void OperationB() => Console.WriteLine("Subsystem B operation");
}

// Facade
public class Facade
{
    private readonly SubsystemA _subsystemA = new();
    private readonly SubsystemB _subsystemB = new();

    public void Operation()
    {
        _subsystemA.OperationA();
        _subsystemB.OperationB();
    }
}

// Usage
class Program
{
    static void Main()
    {
        Facade facade = new Facade();
        facade.Operation();
    }
}
```

---

### **6. Flyweight Pattern**

The **Flyweight Pattern** reduces memory usage by sharing common object state.

#### **C# Example:**

```csharp
// Flyweight
public class Character
{
    public char Symbol { get; }
    public string Font { get; set; }

    public Character(char symbol) => Symbol = symbol;
}

// Flyweight Factory
public class CharacterFactory
{
    private readonly Dictionary<char, Character> _characters = new();

    public Character GetCharacter(char symbol)
    {
        if (!_characters.ContainsKey(symbol))
            _characters[symbol] = new Character(symbol);

        return _characters[symbol];
    }
}

// Usage
class Program
{
    static void Main()
    {
        CharacterFactory factory = new CharacterFactory();

        Character c1 = factory.GetCharacter('A');
        Character c2 = factory.GetCharacter('A'); // Same instance reused

        Console.WriteLine(ReferenceEquals(c1, c2)); // True
    }
}
```

---

### **7. Proxy Pattern**

The **Proxy Pattern** acts as a substitute for another object to control access to it.

#### **C# Example:**

```csharp
// Subject
public interface IService
{
    void Execute();
}

// Real Subject
public class RealService : IService
{
    public void Execute() => Console.WriteLine("Executing real service.");
}

// Proxy
public class ProxyService : IService
{
    private readonly RealService _realService = new();

    public void Execute()
    {
        Console.WriteLine("Proxy controlling access.");
        _realService.Execute();
    }
}

// Usage
class Program
{
    static void Main()
    {
        IService service = new ProxyService();
        service.Execute();
    }
}
```

---

## ✅ **Next Steps**

We have covered **Creational Patterns** and **Structural Patterns**. Next, we will cover **Behavioral Patterns**.

Would you like me to proceed with **Behavioral Patterns**, or do you have any questions so far? 🚀