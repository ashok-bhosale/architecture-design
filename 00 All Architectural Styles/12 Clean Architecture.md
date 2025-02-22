## **.NET Core Application with Clean Architecture**

**Clean Architecture** is a software design pattern that organizes code in a way that enhances maintainability, testability, and scalability. It ensures separation of concerns by dividing the application into different layers.

---

### **📌 Layers in Clean Architecture**

1. **Core Domain (Entities)**
    
    - Contains business logic and domain models.
    - No dependencies on external layers.
2. **Application Layer**
    
    - Contains **use cases** (application services) that orchestrate business logic.
    - Uses interfaces for communication with other layers.
3. **Infrastructure Layer**
    
    - Handles **data access**, **third-party services**, and **APIs**.
    - Implements repositories and external dependencies.
4. **Presentation Layer (API)**
    
    - Exposes **RESTful endpoints**.
    - Uses dependency injection to communicate with the **Application Layer**.

---

## **🔧 Setting Up a .NET Core Clean Architecture Project**

### **📂 Project Structure**

```
/ MyCleanApp
  ├── /Core
  │   ├── /Entities
  │   ├── /Interfaces
  ├── /Application
  │   ├── /Services
  │   ├── /DTOs
  │   ├── /Interfaces
  ├── /Infrastructure
  │   ├── /Persistence (EF Core DB)
  │   ├── /Repositories
  ├── /Presentation (API)
  │   ├── /Controllers
  ├── /Tests
  ├── MyCleanApp.sln
```

---

## **🚀 Step 1: Create the .NET Solution**

```sh
dotnet new sln -n MyCleanApp
dotnet new classlib -n Core
dotnet new classlib -n Application
dotnet new classlib -n Infrastructure
dotnet new webapi -n Presentation
dotnet new xunit -n Tests
dotnet sln add Core Application Infrastructure Presentation Tests
```

---

## **🚀 Step 2: Define Domain Entities**

Inside `Core/Entities/`:

```csharp
namespace Core.Entities
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

---

## **🚀 Step 3: Define Application Interfaces**

Inside `Application/Interfaces/`:

```csharp
using Core.Entities;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Application.Interfaces
{
    public interface IProductService
    {
        Task<IEnumerable<Product>> GetAllProductsAsync();
    }
}
```

---

## **🚀 Step 4: Implement the Service in Application Layer**

Inside `Application/Services/ProductService.cs`:

```csharp
using Application.Interfaces;
using Core.Entities;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Application.Services
{
    public class ProductService : IProductService
    {
        public async Task<IEnumerable<Product>> GetAllProductsAsync()
        {
            // Simulating database fetch
            return await Task.FromResult(new List<Product>
            {
                new Product { Id = 1, Name = "Laptop", Price = 1000 },
                new Product { Id = 2, Name = "Phone", Price = 500 }
            });
        }
    }
}
```

---

## **🚀 Step 5: Implement Infrastructure Layer**

Inside `Infrastructure/Repositories/ProductRepository.cs`:

```csharp
using Application.Interfaces;
using Core.Entities;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Infrastructure.Repositories
{
    public class ProductRepository : IProductService
    {
        public async Task<IEnumerable<Product>> GetAllProductsAsync()
        {
            // Simulated database call
            return await Task.FromResult(new List<Product>
            {
                new Product { Id = 1, Name = "Laptop", Price = 1000 },
                new Product { Id = 2, Name = "Phone", Price = 500 }
            });
        }
    }
}
```

---

## **🚀 Step 6: Register Dependencies in Startup**

Modify `Presentation/Program.cs`:

```csharp
using Application.Interfaces;
using Application.Services;
using Infrastructure.Repositories;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddScoped<IProductService, ProductRepository>(); // Dependency Injection

var app = builder.Build();

app.UseAuthorization();
app.MapControllers();
app.Run();
```

---

## **🚀 Step 7: Create API Controller**

Inside `Presentation/Controllers/ProductController.cs`:

```csharp
using Application.Interfaces;
using Core.Entities;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Threading.Tasks;

[ApiController]
[Route("api/[controller]")]
public class ProductController : ControllerBase
{
    private readonly IProductService _productService;

    public ProductController(IProductService productService)
    {
        _productService = productService;
    }

    [HttpGet]
    public async Task<IEnumerable<Product>> Get()
    {
        return await _productService.GetAllProductsAsync();
    }
}
```

---

## **🚀 Step 8: Run the Application**

```sh
dotnet run --project Presentation
```

### **🖥️ API Endpoint**

- **GET** `http://localhost:5000/api/product`
- Response:

```json
[
  { "id": 1, "name": "Laptop", "price": 1000 },
  { "id": 2, "name": "Phone", "price": 500 }
]
```

---

## **✅ Key Benefits of Clean Architecture**

✔ **Separation of Concerns:** Keeps business logic independent of frameworks.  
✔ **Testability:** Can easily mock dependencies and write unit tests.  
✔ **Scalability:** Easily extend the system by adding new features.  
✔ **Maintainability:** Changes in one layer don’t affect others.

---

## **🚀 Next Steps**

- **Use EF Core for Database**
- **Implement CQRS with MediatR**
- **Add Unit Tests**
- **Secure API with JWT Authentication**
- **Deploy to Azure**

Would you like me to expand on any of these steps? 😊🚀


Let's expand on the next steps in detail.

---

# **🚀 Enhancing Your .NET Core Clean Architecture Project**

Now that we have a basic Clean Architecture setup, let’s take it to the next level with:

1️⃣ **Using EF Core for Database (PostgreSQL/SQL Server)**  
2️⃣ **Implementing CQRS with MediatR**  
3️⃣ **Adding Unit Tests**  
4️⃣ **Securing API with JWT Authentication**  
5️⃣ **Deploying to Azure**

---

## **1️⃣ Using EF Core for Database**

Instead of using in-memory data, let’s connect our application to a database using **Entity Framework Core (EF Core)**.

### **🔹 Step 1: Install EF Core Packages**

Run the following commands in the terminal:

```sh
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

### **🔹 Step 2: Configure the Database Context**

Inside `Infrastructure/Persistence/AppDbContext.cs`:

```csharp
using Core.Entities;
using Microsoft.EntityFrameworkCore;

namespace Infrastructure.Persistence
{
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Product>().HasKey(p => p.Id);
        }
    }
}
```

### **🔹 Step 3: Configure Connection String**

Modify `appsettings.json` in `Presentation/`:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MyCleanDb;User Id=sa;Password=yourpassword;"
  }
}
```

### **🔹 Step 4: Register EF Core in Dependency Injection**

Modify `Program.cs` in `Presentation/`:

```csharp
using Infrastructure.Persistence;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();
app.Run();
```

### **🔹 Step 5: Apply Database Migrations**

Run:

```sh
dotnet ef migrations add InitialCreate
dotnet ef database update
```

Now, your database is set up! 🚀

---

## **2️⃣ Implementing CQRS with MediatR**

CQRS (Command Query Responsibility Segregation) separates **read** and **write** operations, improving performance and scalability.

### **🔹 Step 1: Install MediatR Packages**

```sh
dotnet add package MediatR
dotnet add package MediatR.Extensions.Microsoft.DependencyInjection
```

### **🔹 Step 2: Create CQRS Commands and Queries**

Inside `Application/Products/Queries/GetAllProductsQuery.cs`:

```csharp
using MediatR;
using Core.Entities;
using System.Collections.Generic;

namespace Application.Products.Queries
{
    public class GetAllProductsQuery : IRequest<IEnumerable<Product>> { }
}
```

Inside `Application/Products/Handlers/GetAllProductsHandler.cs`:

```csharp
using MediatR;
using Application.Products.Queries;
using Core.Entities;
using Infrastructure.Persistence;
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using System.Linq;

namespace Application.Products.Handlers
{
    public class GetAllProductsHandler : IRequestHandler<GetAllProductsQuery, IEnumerable<Product>>
    {
        private readonly AppDbContext _context;

        public GetAllProductsHandler(AppDbContext context)
        {
            _context = context;
        }

        public async Task<IEnumerable<Product>> Handle(GetAllProductsQuery request, CancellationToken cancellationToken)
        {
            return await Task.FromResult(_context.Products.ToList());
        }
    }
}
```

### **🔹 Step 3: Modify API Controller**

Inside `ProductController.cs`:

```csharp
using MediatR;
using Application.Products.Queries;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Threading.Tasks;
using Core.Entities;

[ApiController]
[Route("api/[controller]")]
public class ProductController : ControllerBase
{
    private readonly IMediator _mediator;

    public ProductController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpGet]
    public async Task<IEnumerable<Product>> Get()
    {
        return await _mediator.Send(new GetAllProductsQuery());
    }
}
```

### **🔹 Step 4: Register MediatR**

Modify `Program.cs`:

```csharp
using MediatR;
using Application.Products.Handlers;

builder.Services.AddMediatR(typeof(GetAllProductsHandler).Assembly);
```

Now, your application follows the **CQRS pattern** using **MediatR**! 🎯

---

## **3️⃣ Adding Unit Tests**

Unit tests ensure that your business logic works as expected.

### **🔹 Step 1: Install xUnit & Moq**

```sh
dotnet add package xunit
dotnet add package Moq
```

### **🔹 Step 2: Write a Test for ProductService**

Inside `Tests/ProductServiceTests.cs`:

```csharp
using Xunit;
using Moq;
using Application.Interfaces;
using Application.Services;
using Core.Entities;
using System.Collections.Generic;
using System.Threading.Tasks;

public class ProductServiceTests
{
    [Fact]
    public async Task GetAllProductsAsync_ReturnsProducts()
    {
        var mockRepo = new Mock<IProductService>();
        mockRepo.Setup(repo => repo.GetAllProductsAsync()).ReturnsAsync(new List<Product>
        {
            new Product { Id = 1, Name = "Laptop", Price = 1000 }
        });

        var service = new ProductService();
        var result = await service.GetAllProductsAsync();

        Assert.Single(result);
        Assert.Equal("Laptop", result.First().Name);
    }
}
```

Run tests using:

```sh
dotnet test
```

Your tests are now running! ✅

---

## **4️⃣ Securing API with JWT Authentication**

### **🔹 Step 1: Install JWT Packages**

```sh
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

### **🔹 Step 2: Configure JWT in `Program.cs`**

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;

var key = Encoding.ASCII.GetBytes("your_secret_key");

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(key),
            ValidateIssuer = false,
            ValidateAudience = false
        };
    });

var app = builder.Build();
app.UseAuthentication();
app.UseAuthorization();
app.Run();
```

Your API is now secured with JWT! 🔐

---

## **5️⃣ Deploying to Azure**

### **🔹 Step 1: Install Azure CLI**

```sh
az login
```

### **🔹 Step 2: Create an Azure App Service**

```sh
az webapp create --name MyCleanApp --resource-group MyResourceGroup --plan MyAppServicePlan
```

### **🔹 Step 3: Deploy the .NET App**

```sh
dotnet publish -c Release
az webapp deploy --resource-group MyResourceGroup --name MyCleanApp --src-path ./bin/Release/net6.0/publish/
```

Your app is now live on Azure! 🚀🌍

---

## **🎯 Summary**

✅ **EF Core Integration** for database management.  
✅ **CQRS with MediatR** for scalable design.  
✅ **Unit Testing** with xUnit and Moq.  
✅ **JWT Authentication** for security.  
✅ **Azure Deployment** for cloud hosting.

Would you like more details on any step? 😊🚀