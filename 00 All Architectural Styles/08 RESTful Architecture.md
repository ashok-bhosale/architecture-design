
### .NET Core Application with RESTful Architecture

**RESTful Architecture** is an architectural style for designing networked applications. It relies on a stateless, client-server communication model that uses standard HTTP methods (GET, POST, PUT, DELETE) to manage resources, which are represented as URIs (Uniform Resource Identifiers). In REST, communication between clients and servers typically happens in the form of JSON or XML data.

In a **RESTful .NET Core** application, we expose resources via REST APIs that can be consumed by any client (e.g., web browsers, mobile apps, etc.).

---

### Steps to Create a .NET Core Application with RESTful Architecture

#### **Step 1: Create the Project**

1. Open your terminal or command prompt and create a new **.NET Core Web API** project:

```bash
dotnet new webapi -n RestfulApiApp
cd RestfulApiApp
```

2. This will create a base **Web API** project with controllers, a `Startup.cs` file for configuration, and some other essential files.

#### **Step 2: Set Up the Model**

1. **Create a model** for the resource. Let's create a `Product` model:

```csharp
namespace RestfulApiApp.Models
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

The `Product` model represents a resource in our RESTful API. It has properties for the product ID, name, and price.

#### **Step 3: Set Up the Repository (Data Access Layer)**

2. Create an interface for the repository to manage the `Product` data:

```csharp
namespace RestfulApiApp.Repositories
{
    public interface IProductRepository
    {
        IEnumerable<Product> GetAllProducts();
        Product GetProductById(int id);
        void CreateProduct(Product product);
        void UpdateProduct(int id, Product product);
        void DeleteProduct(int id);
    }
}
```

3. Now, create a concrete implementation of the repository:

```csharp
using RestfulApiApp.Models;
using System.Collections.Generic;
using System.Linq;

namespace RestfulApiApp.Repositories
{
    public class ProductRepository : IProductRepository
    {
        private static List<Product> _products = new List<Product>
        {
            new Product { Id = 1, Name = "Laptop", Price = 1000 },
            new Product { Id = 2, Name = "Smartphone", Price = 500 }
        };

        public IEnumerable<Product> GetAllProducts() => _products;

        public Product GetProductById(int id) => _products.FirstOrDefault(p => p.Id == id);

        public void CreateProduct(Product product)
        {
            product.Id = _products.Max(p => p.Id) + 1;
            _products.Add(product);
        }

        public void UpdateProduct(int id, Product product)
        {
            var existingProduct = _products.FirstOrDefault(p => p.Id == id);
            if (existingProduct != null)
            {
                existingProduct.Name = product.Name;
                existingProduct.Price = product.Price;
            }
        }

        public void DeleteProduct(int id)
        {
            var product = _products.FirstOrDefault(p => p.Id == id);
            if (product != null)
            {
                _products.Remove(product);
            }
        }
    }
}
```

In this example, we are using a static list to simulate a database. This would be replaced with a real database in production, using Entity Framework or another ORM.

#### **Step 4: Set Up the Controller**

The controller will handle the HTTP requests and interact with the `ProductRepository` to perform CRUD (Create, Read, Update, Delete) operations.

4. Create a **ProductsController**:

```csharp
using Microsoft.AspNetCore.Mvc;
using RestfulApiApp.Models;
using RestfulApiApp.Repositories;

namespace RestfulApiApp.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductsController : ControllerBase
    {
        private readonly IProductRepository _productRepository;

        public ProductsController(IProductRepository productRepository)
        {
            _productRepository = productRepository;
        }

        // GET api/products
        [HttpGet]
        public IActionResult GetAll()
        {
            var products = _productRepository.GetAllProducts();
            return Ok(products);
        }

        // GET api/products/{id}
        [HttpGet("{id}")]
        public IActionResult Get(int id)
        {
            var product = _productRepository.GetProductById(id);
            if (product == null)
            {
                return NotFound();
            }
            return Ok(product);
        }

        // POST api/products
        [HttpPost]
        public IActionResult Create([FromBody] Product product)
        {
            if (product == null)
            {
                return BadRequest();
            }

            _productRepository.CreateProduct(product);
            return CreatedAtAction(nameof(Get), new { id = product.Id }, product);
        }

        // PUT api/products/{id}
        [HttpPut("{id}")]
        public IActionResult Update(int id, [FromBody] Product product)
        {
            if (product == null)
            {
                return BadRequest();
            }

            _productRepository.UpdateProduct(id, product);
            return NoContent();
        }

        // DELETE api/products/{id}
        [HttpDelete("{id}")]
        public IActionResult Delete(int id)
        {
            _productRepository.DeleteProduct(id);
            return NoContent();
        }
    }
}
```

In this controller:

- The `GET` methods fetch product data.
- The `POST` method creates a new product.
- The `PUT` method updates an existing product.
- The `DELETE` method removes a product.

#### **Step 5: Configure Dependency Injection**

To use the repository in the controller, we need to register it in the `Startup.cs` class:

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        // Register the repository with dependency injection
        services.AddSingleton<IProductRepository, ProductRepository>();
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

Here, we are using `AddSingleton` to register the repository. This is a simple application, so it's okay to use `Singleton` for now. However, for more complex scenarios, you might want to use `Scoped` or `Transient`.

#### **Step 6: Run the Application**

5. Run the application:

```bash
dotnet run
```

6. The application will start, and you can access the API endpoints:
    - `GET /api/products` — List all products.
    - `GET /api/products/{id}` — Get a specific product by ID.
    - `POST /api/products` — Create a new product.
    - `PUT /api/products/{id}` — Update an existing product.
    - `DELETE /api/products/{id}` — Delete a product.

You can test these endpoints using **Postman** or **Swagger** (which comes with the default Web API template in .NET Core).

#### **Step 7: Optional Enhancements**

7. **Error Handling**: Add custom error handling and validation logic.
8. **Authentication and Authorization**: Add security features like JWT tokens to secure the API.
9. **Swagger**: Integrate Swagger for API documentation. In the `Startup.cs`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSwaggerGen();
    services.AddControllers();
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseSwagger();
    app.UseSwaggerUI();

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Now, you can access Swagger at `http://localhost:5000/swagger` to see all your API endpoints.

---

### **Summary**

In this tutorial, we've created a **.NET Core** application using **RESTful architecture**. We:

- Set up a simple `Product` model.
- Created a `ProductRepository` to simulate data access.
- Implemented a `ProductsController` to handle HTTP requests.
- Configured Dependency Injection for the repository.
- Tested the application with basic CRUD operations.

This RESTful API architecture allows for scalable, maintainable, and easily accessible services for client applications.