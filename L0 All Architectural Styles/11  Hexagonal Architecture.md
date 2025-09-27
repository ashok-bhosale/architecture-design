### **.NET Core Application with Hexagonal Architecture (Ports and Adapters)**

**Hexagonal Architecture**, also known as **Ports and Adapters**, ensures that the core business logic (domain) is independent of frameworks, databases, and external services. It promotes testability, maintainability, and flexibility.

---

## **1. Key Components**

1. **Domain Layer** (Core Business Logic)
2. **Application Layer** (Use Cases, Services)
3. **Adapters** (Infrastructure, External Services)
4. **Ports** (Interfaces for input/output interactions)
5. **Infrastructure Layer** (Database, API, External Services)
6. **Presentation Layer** (Web API or UI)

---

## **2. Project Structure**

```
HexagonalApp/
│── HexagonalApp.Domain/         → Business Logic (Entities, Interfaces)
│── HexagonalApp.Application/    → Use Cases, Services
│── HexagonalApp.Infrastructure/ → Repositories, External Services
│── HexagonalApp.API/            → Web API (Controllers)
│── HexagonalApp.Tests/          → Unit and Integration Tests
│── HexagonalApp.sln
```

---

## **3. Implementing Hexagonal Architecture in .NET Core**

### **Step 1: Create a Solution**

```sh
dotnet new sln -n HexagonalApp
dotnet new classlib -n HexagonalApp.Domain
dotnet new classlib -n HexagonalApp.Application
dotnet new classlib -n HexagonalApp.Infrastructure
dotnet new webapi -n HexagonalApp.API
dotnet new xunit -n HexagonalApp.Tests
```

---

### **Step 2: Define the Domain Layer**

#### **HexagonalApp.Domain/Entities/Order.cs**

```csharp
namespace HexagonalApp.Domain.Entities
{
    public class Order
    {
        public int Id { get; set; }
        public string ProductName { get; set; }
        public int Quantity { get; set; }
    }
}
```

#### **HexagonalApp.Domain/Ports/IOrderRepository.cs (Ports)**

```csharp
namespace HexagonalApp.Domain.Ports
{
    public interface IOrderRepository
    {
        Task<IEnumerable<Order>> GetAllOrdersAsync();
        Task<Order> GetOrderByIdAsync(int id);
        Task AddOrderAsync(Order order);
    }
}
```

---

### **Step 3: Implement the Application Layer**

#### **HexagonalApp.Application/Services/OrderService.cs**

```csharp
using HexagonalApp.Domain.Entities;
using HexagonalApp.Domain.Ports;

namespace HexagonalApp.Application.Services
{
    public class OrderService
    {
        private readonly IOrderRepository _orderRepository;

        public OrderService(IOrderRepository orderRepository)
        {
            _orderRepository = orderRepository;
        }

        public async Task<IEnumerable<Order>> GetOrdersAsync()
        {
            return await _orderRepository.GetAllOrdersAsync();
        }

        public async Task<Order> GetOrderByIdAsync(int id)
        {
            return await _orderRepository.GetOrderByIdAsync(id);
        }

        public async Task AddOrderAsync(Order order)
        {
            await _orderRepository.AddOrderAsync(order);
        }
    }
}
```

---

### **Step 4: Implement the Infrastructure Layer (Adapters)**

#### **HexagonalApp.Infrastructure/Adapters/OrderRepository.cs**

```csharp
using System.Collections.Generic;
using System.Threading.Tasks;
using HexagonalApp.Domain.Entities;
using HexagonalApp.Domain.Ports;

namespace HexagonalApp.Infrastructure.Adapters
{
    public class OrderRepository : IOrderRepository
    {
        private static readonly List<Order> _orders = new();

        public async Task<IEnumerable<Order>> GetAllOrdersAsync()
        {
            return await Task.FromResult(_orders);
        }

        public async Task<Order> GetOrderByIdAsync(int id)
        {
            return await Task.FromResult(_orders.Find(o => o.Id == id));
        }

        public async Task AddOrderAsync(Order order)
        {
            _orders.Add(order);
            await Task.CompletedTask;
        }
    }
}
```

---

### **Step 5: Implement the Web API Layer (Adapters)**

#### **HexagonalApp.API/Controllers/OrderController.cs**

```csharp
using HexagonalApp.Application.Services;
using HexagonalApp.Domain.Entities;
using Microsoft.AspNetCore.Mvc;

namespace HexagonalApp.API.Controllers
{
    [ApiController]
    [Route("api/orders")]
    public class OrderController : ControllerBase
    {
        private readonly OrderService _orderService;

        public OrderController(OrderService orderService)
        {
            _orderService = orderService;
        }

        [HttpGet]
        public async Task<IActionResult> GetOrders()
        {
            var orders = await _orderService.GetOrdersAsync();
            return Ok(orders);
        }

        [HttpPost]
        public async Task<IActionResult> AddOrder(Order order)
        {
            await _orderService.AddOrderAsync(order);
            return CreatedAtAction(nameof(GetOrders), new { id = order.Id }, order);
        }
    }
}
```

---

### **Step 6: Register Dependencies in DI Container**

#### **HexagonalApp.API/Program.cs**

```csharp
using HexagonalApp.Application.Services;
using HexagonalApp.Domain.Ports;
using HexagonalApp.Infrastructure.Adapters;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddSingleton<IOrderRepository, OrderRepository>();
builder.Services.AddTransient<OrderService>();

builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

app.UseSwagger();
app.UseSwaggerUI();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

---

### **7. Run and Test the API**

1. Run the API:
    
    ```sh
    dotnet run --project HexagonalApp.API
    ```
    
2. Test with **Postman** or **cURL**:
    
    ```sh
    curl -X POST http://localhost:5000/api/orders -H "Content-Type: application/json" -d '{"id":1, "productName":"Laptop", "quantity":2}'
    curl -X GET http://localhost:5000/api/orders
    ```
    

---

## **8. Summary**

✅ **Hexagonal Architecture (Ports & Adapters)**

- **Domain Layer**: Core business logic, entities, and ports (interfaces).
- **Application Layer**: Services (use cases) implementing domain logic.
- **Infrastructure Layer**: Adapters (database, API, external services).
- **Presentation Layer**: Web API using controllers.

Would you like to extend this with a **real database, event-driven communication, or microservices integration**? 🚀