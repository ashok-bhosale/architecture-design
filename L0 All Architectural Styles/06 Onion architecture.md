### **.NET Core Application with Onion Architecture**

**Onion Architecture** is a layered architecture that aims to address common problems in traditional layered architectures like tight coupling and separation of concerns. The main idea behind Onion Architecture is to isolate the core business logic and keep it independent from external dependencies such as UI, data access, and frameworks.

In **Onion Architecture**, the application is divided into several concentric layers, with each layer depending only on the innermost layer, not the outer layers. This results in a system that is highly maintainable, testable, and flexible to change.

---

### **Onion Architecture Layers:**

1. **Core (Domain) Layer**: This is the innermost layer, which contains the core business logic of the application. It consists of:
    
    - **Entities**: Represents the business objects.
    - **Interfaces/Contracts**: Interfaces for interacting with other layers.
    - **Domain Services**: Business logic and rules that don't belong to a specific entity.
2. **Application Layer**: This layer contains use cases and application services. It defines the operations that are available in the application and orchestrates the flow between the **Domain Layer** and the **Infrastructure Layer**.
    
3. **Infrastructure Layer**: This layer implements the interfaces defined in the **Application Layer** and provides access to external systems such as databases, file systems, APIs, etc. It typically includes repositories, database context classes, and external service integrations.
    
4. **Presentation Layer**: This is the outermost layer and interacts with the user. In a web application, it would be responsible for handling HTTP requests, performing validation, and rendering views. In a .NET Core MVC application, this layer consists of controllers and views.
    

---

### **Steps to Create a .NET Core Application with Onion Architecture**

#### **Step 1: Set Up the Project Structure**

Create the following solution and project structure:

```plaintext
MyApplication
│
├── MyApplication.Core        # Domain Layer
│   ├── Entities
│   ├── Interfaces
│   └── Services
│
├── MyApplication.Application # Application Layer
│   └── Services
│
├── MyApplication.Infrastructure # Infrastructure Layer
│   ├── Data
│   └── Repositories
│
└── MyApplication.API           # Presentation Layer (API layer)
    ├── Controllers
    └── Models
```

#### **Step 2: Implement Core (Domain) Layer**

The **Core** layer holds the fundamental business logic.

1. **Entities**: Create a `Product` entity in the **Core** layer.

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

2. **Interfaces**: Define interfaces for repositories or services to abstract the interaction with data or business logic.

```csharp
namespace MyApplication.Core.Interfaces
{
    public interface IProductRepository
    {
        IEnumerable<Product> GetAllProducts();
        Product GetProductById(int id);
        void AddProduct(Product product);
    }
}
```

1. **Services**: Define business logic or domain services if needed.

```csharp
namespace MyApplication.Core.Services
{
    public class ProductService
    {
        private readonly IProductRepository _productRepository;

        public ProductService(IProductRepository productRepository)
        {
            _productRepository = productRepository;
        }

        public IEnumerable<Product> GetProducts()
        {
            return _productRepository.GetAllProducts();
        }

        public Product GetProductById(int id)
        {
            return _productRepository.GetProductById(id);
        }

        public void AddProduct(Product product)
        {
            _productRepository.AddProduct(product);
        }
    }
}
```

#### **Step 3: Implement Application Layer**

The **Application** layer contains the use cases and orchestrates the flow between the core and the infrastructure.

2. **Application Services**: Create an application service that interacts with the core layer’s domain service.

```csharp
namespace MyApplication.Application.Services
{
    public class ProductAppService
    {
        private readonly ProductService _productService;

        public ProductAppService(ProductService productService)
        {
            _productService = productService;
        }

        public IEnumerable<Product> GetAllProducts()
        {
            return _productService.GetProducts();
        }

        public Product GetProduct(int id)
        {
            return _productService.GetProductById(id);
        }

        public void CreateProduct(Product product)
        {
            _productService.AddProduct(product);
        }
    }
}
```

#### **Step 4: Implement Infrastructure Layer**

The **Infrastructure** layer implements the interfaces defined in the **Core** layer and interacts with external resources like databases.

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

        public IEnumerable<Product> GetAllProducts()
        {
            return _context.Products.ToList();
        }

        public Product GetProductById(int id)
        {
            return _context.Products.Find(id);
        }

        public void AddProduct(Product product)
        {
            _context.Products.Add(product);
            _context.SaveChanges();
        }
    }
}
```

4. **Database Context**: Define the **DbContext** for accessing the database (if using **Entity Framework Core**).

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

The **Presentation** layer handles HTTP requests and communicates with the **Application** layer.

5. **Controllers**: Create an API controller that consumes the application service.

```csharp
using Microsoft.AspNetCore.Mvc;
using MyApplication.Application.Services;
using MyApplication.Core.Entities;

namespace MyApplication.API.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductsController : ControllerBase
    {
        private readonly ProductAppService _productAppService;

        public ProductsController(ProductAppService productAppService)
        {
            _productAppService = productAppService;
        }

        [HttpGet]
        public IActionResult GetAll()
        {
            var products = _productAppService.GetAllProducts();
            return Ok(products);
        }

        [HttpGet("{id}")]
        public IActionResult Get(int id)
        {
            var product = _productAppService.GetProduct(id);
            if (product == null)
                return NotFound();
            return Ok(product);
        }

        [HttpPost]
        public IActionResult Create([FromBody] Product product)
        {
            _productAppService.CreateProduct(product);
            return CreatedAtAction(nameof(Get), new { id = product.Id }, product);
        }
    }
}
```

#### **Step 6: Configure Dependency Injection (DI)**

In **Startup.cs**, configure the dependency injection for the various services and repositories.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
        
    services.AddScoped<IProductRepository, ProductRepository>();
    services.AddScoped<ProductService>();
    services.AddScoped<ProductAppService>();

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

In this example, we built a simple **.NET Core** application using **Onion Architecture**. The Onion Architecture separates the application into several layers:

6. **Core (Domain) Layer**: Contains the business logic (entities, services, and interfaces).
7. **Application Layer**: Contains the application services that interact with the core layer and orchestrate business logic.
8. **Infrastructure Layer**: Implements interfaces and interacts with external systems like databases.
9. **Presentation Layer**: Handles HTTP requests and serves the UI or API.

By organizing the application in this way, we achieve loose coupling between the layers, which makes the application more maintainable, testable, and flexible to future changes.