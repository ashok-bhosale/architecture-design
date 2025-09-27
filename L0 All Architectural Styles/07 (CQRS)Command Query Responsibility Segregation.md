### **.NET Core Application with Command Query Responsibility Segregation (CQRS)**

**CQRS** (Command Query Responsibility Segregation) is a pattern that separates read and write operations into different models. The idea behind CQRS is to have one model for updating data (commands) and a separate model for reading data (queries). This separation allows for optimization of read and write operations independently and can help improve scalability and maintainability, especially in complex applications.

In a **CQRS**-based system, the write side (commands) and the read side (queries) are treated as independent components with their own data models, business logic, and often their own storage. This separation enables more flexibility in optimizing each operation.

---

### **CQRS Architecture Layers**

1. **Command Layer**:
    
    - Handles write operations.
    - Contains command handlers that define the logic for performing updates.
2. **Query Layer**:
    
    - Handles read operations.
    - Contains query handlers that define the logic for retrieving data in a read-optimized manner.
3. **Domain Model**:
    
    - Contains the core business logic and domain entities.
    - This layer can be shared between the command and query sides, but in some cases, separate models can be used for read and write.
4. **Persistence Layer**:
    
    - Each side (command and query) may have its own data store.
    - The command side will typically work with an event store or a transactional database, while the query side might use a read-optimized database or cache.

---

### **Steps to Create a .NET Core Application with CQRS**

#### **Step 1: Set Up the Project Structure**

Create the following solution and project structure for CQRS:

```plaintext
MyApplication
│
├── MyApplication.Core        # Domain Model
│   ├── Entities
│   ├── Interfaces
│   └── Services
│
├── MyApplication.Application # Command & Query Handlers
│   ├── Commands
│   ├── CommandHandlers
│   ├── Queries
│   └── QueryHandlers
│
├── MyApplication.Infrastructure # Data Access (Command & Query Repositories)
│   ├── Data
│   └── Repositories
│
└── MyApplication.API          # Presentation Layer (API)
    ├── Controllers
    └── Models
```

#### **Step 2: Implement Core (Domain) Layer**

1. **Entities**: Create a `Product` entity for your application.

```csharp
namespace MyApplication.Core.Entities
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

2. **Interfaces**: Define the interfaces that will be implemented by the repositories.

```csharp
namespace MyApplication.Core.Interfaces
{
    public interface IProductRepository
    {
        void AddProduct(Product product);
        Product GetProductById(int id);
        IEnumerable<Product> GetAllProducts();
    }
}
```

#### **Step 3: Implement Application Layer (Command and Query Handlers)**

1. **Commands**: Define commands that will be used to make changes to the data.

```csharp
namespace MyApplication.Application.Commands
{
    public class CreateProductCommand
    {
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

2. **Command Handlers**: Implement command handlers to handle write operations.

```csharp
using MyApplication.Application.Commands;
using MyApplication.Core.Entities;
using MyApplication.Core.Interfaces;

namespace MyApplication.Application.CommandHandlers
{
    public class CreateProductCommandHandler
    {
        private readonly IProductRepository _productRepository;

        public CreateProductCommandHandler(IProductRepository productRepository)
        {
            _productRepository = productRepository;
        }

        public void Handle(CreateProductCommand command)
        {
            var product = new Product
            {
                Name = command.Name,
                Price = command.Price
            };

            _productRepository.AddProduct(product);
        }
    }
}
```

1. **Queries**: Define queries to retrieve data.

```csharp
namespace MyApplication.Application.Queries
{
    public class GetProductQuery
    {
        public int ProductId { get; set; }
    }

    public class GetAllProductsQuery { }
}
```

2. **Query Handlers**: Implement query handlers to handle read operations.

```csharp
using MyApplication.Application.Queries;
using MyApplication.Core.Entities;
using MyApplication.Core.Interfaces;

namespace MyApplication.Application.QueryHandlers
{
    public class GetProductQueryHandler
    {
        private readonly IProductRepository _productRepository;

        public GetProductQueryHandler(IProductRepository productRepository)
        {
            _productRepository = productRepository;
        }

        public Product Handle(GetProductQuery query)
        {
            return _productRepository.GetProductById(query.ProductId);
        }
    }

    public class GetAllProductsQueryHandler
    {
        private readonly IProductRepository _productRepository;

        public GetAllProductsQueryHandler(IProductRepository productRepository)
        {
            _productRepository = productRepository;
        }

        public IEnumerable<Product> Handle(GetAllProductsQuery query)
        {
            return _productRepository.GetAllProducts();
        }
    }
}
```

#### **Step 4: Implement Infrastructure Layer (Repositories)**

3. **Repositories**: Implement the `IProductRepository` interface.

```csharp
using MyApplication.Core.Entities;
using MyApplication.Core.Interfaces;
using Microsoft.EntityFrameworkCore;

namespace MyApplication.Infrastructure.Repositories
{
    public class ProductRepository : IProductRepository
    {
        private readonly ApplicationDbContext _context;

        public ProductRepository(ApplicationDbContext context)
        {
            _context = context;
        }

        public void AddProduct(Product product)
        {
            _context.Products.Add(product);
            _context.SaveChanges();
        }

        public Product GetProductById(int id)
        {
            return _context.Products.Find(id);
        }

        public IEnumerable<Product> GetAllProducts()
        {
            return _context.Products.ToList();
        }
    }
}
```

4. **Database Context**: Define the **DbContext** for accessing the database (using **Entity Framework Core**).

```csharp
using Microsoft.EntityFrameworkCore;
using MyApplication.Core.Entities;

namespace MyApplication.Infrastructure.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }

        public DbSet<Product> Products { get; set; }
    }
}
```

#### **Step 5: Implement Presentation Layer (API)**

5. **Controllers**: Create an API controller that consumes the command and query handlers.

```csharp
using Microsoft.AspNetCore.Mvc;
using MyApplication.Application.Commands;
using MyApplication.Application.CommandHandlers;
using MyApplication.Application.Queries;
using MyApplication.Application.QueryHandlers;
using MyApplication.Core.Entities;

namespace MyApplication.API.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductsController : ControllerBase
    {
        private readonly CreateProductCommandHandler _createProductHandler;
        private readonly GetProductQueryHandler _getProductHandler;
        private readonly GetAllProductsQueryHandler _getAllProductsHandler;

        public ProductsController(
            CreateProductCommandHandler createProductHandler,
            GetProductQueryHandler getProductHandler,
            GetAllProductsQueryHandler getAllProductsHandler)
        {
            _createProductHandler = createProductHandler;
            _getProductHandler = getProductHandler;
            _getAllProductsHandler = getAllProductsHandler;
        }

        [HttpPost]
        public IActionResult Create([FromBody] CreateProductCommand command)
        {
            _createProductHandler.Handle(command);
            return Ok();
        }

        [HttpGet("{id}")]
        public IActionResult Get(int id)
        {
            var query = new GetProductQuery { ProductId = id };
            var product = _getProductHandler.Handle(query);

            if (product == null)
                return NotFound();

            return Ok(product);
        }

        [HttpGet]
        public IActionResult GetAll()
        {
            var query = new GetAllProductsQuery();
            var products = _getAllProductsHandler.Handle(query);
            return Ok(products);
        }
    }
}
```

#### **Step 6: Configure Dependency Injection (DI)**

In **Startup.cs**, configure the dependency injection for the command and query handlers.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
        
    services.AddScoped<IProductRepository, ProductRepository>();
    services.AddScoped<CreateProductCommandHandler>();
    services.AddScoped<GetProductQueryHandler>();
    services.AddScoped<GetAllProductsQueryHandler>();

    services.AddControllers();
}
```

#### **Step 7: Run the Application**

Finally, run the application using the command:

```bash
dotnet run
```

You can now access the API at `http://localhost:5000/api/products`.

---

### **Summary**

In this example, we built a simple **.NET Core** application using **CQRS**. The application consists of separate layers for handling commands and queries:

6. **Command Layer**: Contains the logic for creating, updating, or deleting data.
7. **Query Layer**: Contains the logic for retrieving data.
8. **Domain Model**: Contains the core business logic and entities.
9. **Presentation Layer**: Handles HTTP requests and serves the UI or API.

By separating the read and write operations, CQRS enables better scalability and flexibility for complex systems, making it a useful pattern for large, high-performance applications.