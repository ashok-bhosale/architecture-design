
### .NET Core Application with **Service-Oriented Architecture (SOA)**

**Service-Oriented Architecture (SOA)** is an architectural pattern that structures an application as a collection of loosely coupled, independent services. Each service in SOA is designed to perform a specific business function and is typically designed to interact with other services through well-defined APIs. SOA aims to provide reusability, scalability, and flexibility in building complex enterprise applications.

In **.NET Core**, we can implement SOA using microservices, where each service is independent and communicates with other services via HTTP, messaging, or other protocols.

---

### Steps to Create a .NET Core Application with **Service-Oriented Architecture (SOA)**

#### **Step 1: Create the Project**

1. Open the terminal and create a new **.NET Core Web API** project for the service layer:

```bash
dotnet new webapi -n OrderService
cd OrderService
```

2. This will create the **Order Service** API that is part of a larger SOA system. Later, we'll create multiple services like `CustomerService`, `ProductService`, etc.

#### **Step 2: Set Up the Service Layer**

Define the service layer where each service represents a specific business domain (e.g., `Order`, `Customer`, `Product`).

For the **OrderService**, we can define a model:

```csharp
namespace OrderService.Models
{
    public class Order
    {
        public int Id { get; set; }
        public string ProductName { get; set; }
        public int Quantity { get; set; }
        public decimal Price { get; set; }
    }
}
```

#### **Step 3: Implement Business Logic**

In SOA, the business logic is encapsulated in services. Create a service interface and its implementation.

**Order Service Interface:**

```csharp
namespace OrderService.Services
{
    public interface IOrderService
    {
        IEnumerable<Order> GetAllOrders();
        Order GetOrderById(int id);
        void CreateOrder(Order order);
    }
}
```

**Order Service Implementation:**

```csharp
namespace OrderService.Services
{
    public class OrderService : IOrderService
    {
        private static List<Order> _orders = new List<Order>
        {
            new Order { Id = 1, ProductName = "Laptop", Quantity = 1, Price = 1000 },
            new Order { Id = 2, ProductName = "Phone", Quantity = 2, Price = 500 }
        };

        public IEnumerable<Order> GetAllOrders() => _orders;

        public Order GetOrderById(int id) => _orders.FirstOrDefault(o => o.Id == id);

        public void CreateOrder(Order order)
        {
            order.Id = _orders.Max(o => o.Id) + 1;
            _orders.Add(order);
        }
    }
}
```

#### **Step 4: Create the Controller**

Create a controller that will expose the service's functionality as HTTP APIs.

```csharp
using Microsoft.AspNetCore.Mvc;
using OrderService.Models;
using OrderService.Services;

namespace OrderService.Controllers
{
    [Route("api/orders")]
    [ApiController]
    public class OrdersController : ControllerBase
    {
        private readonly IOrderService _orderService;

        public OrdersController(IOrderService orderService)
        {
            _orderService = orderService;
        }

        // GET api/orders
        [HttpGet]
        public IActionResult GetAllOrders()
        {
            var orders = _orderService.GetAllOrders();
            return Ok(orders);
        }

        // GET api/orders/{id}
        [HttpGet("{id}")]
        public IActionResult GetOrderById(int id)
        {
            var order = _orderService.GetOrderById(id);
            if (order == null)
            {
                return NotFound();
            }
            return Ok(order);
        }

        // POST api/orders
        [HttpPost]
        public IActionResult CreateOrder([FromBody] Order order)
        {
            if (order == null)
            {
                return BadRequest();
            }
            _orderService.CreateOrder(order);
            return CreatedAtAction(nameof(GetOrderById), new { id = order.Id }, order);
        }
    }
}
```

#### **Step 5: Add Dependency Injection**

Register the service and repository in the `Startup.cs` for dependency injection:

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
        services.AddSingleton<IOrderService, OrderService.Services.OrderService>();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
}
```

#### **Step 6: Add Other Services (e.g., Customer Service, Product Service)**

You can create other services in the same way. For example, a **Customer Service** can be created to handle customer-related operations, and a **Product Service** can manage product information.

Each of these services will have its own controllers and interfaces but will work independently, following the **SOA principles**.

---

### **Step 7: Service Communication (Optional)**

In **SOA**, services communicate with each other via well-defined APIs. You can use different protocols for service communication, such as:

- **HTTP/REST**: Services can communicate over HTTP using RESTful APIs.
- **Message Brokers**: Use **message brokers** like **RabbitMQ** or **Kafka** for asynchronous communication.
- **gRPC**: gRPC can be used for high-performance communication between microservices.

For communication, the `OrderService` might interact with a **CustomerService** to fetch customer details.

**Example: Service Communication via HTTP (Customer Service)**

In the `OrderService`, create a method to call the `CustomerService` API:

```csharp
public class OrderService : IOrderService
{
    private readonly IHttpClientFactory _clientFactory;

    public OrderService(IHttpClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }

    public async Task<Order> GetOrderWithCustomerDetails(int orderId)
    {
        // Get order details (simulated here)
        var order = GetOrderById(orderId);
        
        // Call CustomerService API to get customer details
        var client = _clientFactory.CreateClient();
        var response = await client.GetAsync($"http://customer-service/api/customers/{order.CustomerId}");
        var customer = await response.Content.ReadAsAsync<Customer>();

        // Add customer data to order
        order.Customer = customer;
        return order;
    }
}
```

You would need to configure **HttpClientFactory** and call the **CustomerService** API accordingly.

---

### **Step 8: Test the Application**

1. Run the **OrderService** API:

```bash
dotnet run
```

2. The application will start, and you can access the following endpoints:

- `GET /api/orders` — Retrieve all orders.
- `GET /api/orders/{id}` — Retrieve a specific order.
- `POST /api/orders` — Create a new order.

You can extend this by adding other services like **CustomerService**, **ProductService**, and implement communication between these services.

---

### **Step 9: Scaling SOA**

To scale the architecture, you can:

1. **Decouple services**: Each service should be independently deployable and scalable.
2. **Use Load Balancers**: Deploy multiple instances of services and use load balancers to distribute requests.
3. **Implement Service Discovery**: In a distributed environment, use service discovery mechanisms to find available services.

---

### **Summary**

In this tutorial, we've built a basic **.NET Core application** following **Service-Oriented Architecture (SOA)** principles. Key aspects of SOA in this implementation include:

- Independent **services** (e.g., `OrderService`).
- **Service Communication** through HTTP (REST APIs).
- **Scalable** services that can be independently deployed and managed.
- **Business logic** is encapsulated within services to maintain loose coupling and flexibility.

You can extend this architecture by adding more services, enhancing communication methods (e.g., message queues), and implementing service discovery mechanisms for large-scale applications.