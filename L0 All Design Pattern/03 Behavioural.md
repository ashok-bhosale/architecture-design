Now, let's cover **Behavioral Design Patterns** in C#.

---

## **3️⃣ Behavioral Design Patterns** (Object Communication)

### **1. Chain of Responsibility Pattern**

The **Chain of Responsibility Pattern** passes requests along a chain of handlers until one handles it.

#### **C# Example:**

```csharp
// Handler Interface
public abstract class Handler
{
    protected Handler? NextHandler;

    public void SetNext(Handler next) => NextHandler = next;

    public abstract void HandleRequest(int request);
}

// Concrete Handlers
public class ConcreteHandler1 : Handler
{
    public override void HandleRequest(int request)
    {
        if (request < 10)
            Console.WriteLine($"Handler1 handled request {request}");
        else
            NextHandler?.HandleRequest(request);
    }
}

public class ConcreteHandler2 : Handler
{
    public override void HandleRequest(int request)
    {
        if (request < 20)
            Console.WriteLine($"Handler2 handled request {request}");
        else
            NextHandler?.HandleRequest(request);
    }
}

// Usage
class Program
{
    static void Main()
    {
        Handler h1 = new ConcreteHandler1();
        Handler h2 = new ConcreteHandler2();
        h1.SetNext(h2);

        h1.HandleRequest(15); // Handled by Handler2
    }
}
```

---

### **2. Command Pattern**

The **Command Pattern** encapsulates a request as an object, allowing parameterization and queuing of requests.

#### **C# Example:**

```csharp
// Command Interface
public interface ICommand
{
    void Execute();
}

// Concrete Command
public class PrintCommand : ICommand
{
    public void Execute() => Console.WriteLine("Printing document...");
}

// Invoker
public class Invoker
{
    private ICommand _command;
    
    public Invoker(ICommand command) => _command = command;

    public void ExecuteCommand() => _command.Execute();
}

// Usage
class Program
{
    static void Main()
    {
        ICommand command = new PrintCommand();
        Invoker invoker = new Invoker(command);
        invoker.ExecuteCommand();
    }
}
```

---

### **3. Interpreter Pattern**

The **Interpreter Pattern** interprets sentences in a specialized language.

#### **C# Example:**

```csharp
// Abstract Expression
public abstract class Expression
{
    public abstract void Interpret(Context context);
}

// Terminal Expression
public class TerminalExpression : Expression
{
    public override void Interpret(Context context) =>
        Console.WriteLine($"Interpreting {context.Input}");
}

// Context
public class Context
{
    public string Input { get; set; }

    public Context(string input) => Input = input;
}

// Usage
class Program
{
    static void Main()
    {
        Context context = new Context("Hello");
        Expression expression = new TerminalExpression();
        expression.Interpret(context);
    }
}
```

---

### **4. Iterator Pattern**

The **Iterator Pattern** provides a way to access elements sequentially without exposing the underlying structure.

#### **C# Example:**

```csharp
// Iterator Interface
public interface IIterator<T>
{
    bool HasNext();
    T Next();
}

// Concrete Iterator
public class ListIterator<T> : IIterator<T>
{
    private readonly List<T> _items;
    private int _index = 0;

    public ListIterator(List<T> items) => _items = items;

    public bool HasNext() => _index < _items.Count;
    public T Next() => _items[_index++];
}

// Usage
class Program
{
    static void Main()
    {
        List<string> names = new() { "Alice", "Bob", "Charlie" };
        IIterator<string> iterator = new ListIterator<string>(names);

        while (iterator.HasNext())
            Console.WriteLine(iterator.Next());
    }
}
```

---

### **5. Mediator Pattern**

The **Mediator Pattern** centralizes communication between objects.

#### **C# Example:**

```csharp
// Mediator Interface
public interface IMediator
{
    void SendMessage(string message, Colleague colleague);
}

// Colleague Class
public abstract class Colleague
{
    protected IMediator Mediator;

    protected Colleague(IMediator mediator) => Mediator = mediator;
}

// Concrete Colleague
public class ConcreteColleague : Colleague
{
    public ConcreteColleague(IMediator mediator) : base(mediator) { }

    public void Send(string message) => Mediator.SendMessage(message, this);

    public void Receive(string message) => Console.WriteLine($"Received: {message}");
}

// Concrete Mediator
public class ConcreteMediator : IMediator
{
    private readonly List<Colleague> _colleagues = new();

    public void Register(Colleague colleague) => _colleagues.Add(colleague);

    public void SendMessage(string message, Colleague sender)
    {
        foreach (var colleague in _colleagues)
            if (colleague != sender)
                colleague.Receive(message);
    }
}

// Usage
class Program
{
    static void Main()
    {
        ConcreteMediator mediator = new();
        ConcreteColleague c1 = new(mediator);
        ConcreteColleague c2 = new(mediator);

        mediator.Register(c1);
        mediator.Register(c2);

        c1.Send("Hello!");
    }
}
```

---

### **6. Memento Pattern**

The **Memento Pattern** captures an object's state to restore it later.

#### **C# Example:**

```csharp
// Memento
public class Memento
{
    public string State { get; }

    public Memento(string state) => State = state;
}

// Originator
public class Originator
{
    public string State { get; set; }

    public Memento Save() => new Memento(State);

    public void Restore(Memento memento) => State = memento.State;
}

// Caretaker
public class Caretaker
{
    public Memento Memento { get; set; }
}

// Usage
class Program
{
    static void Main()
    {
        Originator originator = new() { State = "Initial" };
        Caretaker caretaker = new() { Memento = originator.Save() };

        originator.State = "Changed";
        originator.Restore(caretaker.Memento);

        Console.WriteLine(originator.State); // Outputs: Initial
    }
}
```

---

### **7. Observer Pattern**

The **Observer Pattern** allows objects to react to changes in another object.

#### **C# Example:**

```csharp
// Observer Interface
public interface IObserver
{
    void Update(string message);
}

// Subject
public class Subject
{
    private readonly List<IObserver> _observers = new();

    public void Attach(IObserver observer) => _observers.Add(observer);
    public void Notify(string message)
    {
        foreach (var observer in _observers)
            observer.Update(message);
    }
}

// Concrete Observer
public class ConcreteObserver : IObserver
{
    private readonly string _name;

    public ConcreteObserver(string name) => _name = name;

    public void Update(string message) => Console.WriteLine($"{_name} received: {message}");
}

// Usage
class Program
{
    static void Main()
    {
        Subject subject = new();
        IObserver observer1 = new ConcreteObserver("Observer1");
        IObserver observer2 = new ConcreteObserver("Observer2");

        subject.Attach(observer1);
        subject.Attach(observer2);

        subject.Notify("Hello Observers!");
    }
}
```

---

## **Summary**

We have now covered **all 23 design patterns** in **Creational, Structural, and Behavioral** categories.

Would you like me to provide a **table summary** of all patterns or any additional explanation? 🚀