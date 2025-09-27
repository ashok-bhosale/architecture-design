### **.NET Core Application with Separation of Concerns (SoC)**

**Separation of Concerns (SoC)** is a design principle for software development that encourages separating a program into distinct sections, where each section addresses a separate concern or responsibility. The goal is to keep each module or layer of the application independent and focused on a single aspect of the system, making the code **more maintainable, testable**, and **scalable**.

In a .NET Core application, this principle can be applied by dividing the application into layers or components, where each layer handles a specific type of responsibility. Common layers in SoC are typically:

1. **Presentation Layer** – Deals with user interaction (e.g., controllers in MVC or API endpoints).
2. **Business Logic Layer** – Handles the core application logic.
3. **Data Access Layer** – Handles interaction with databases and other external systems.
4. **Service Layer** – Bridges between the business logic and data access layers (optional in some architectures).

Let’s look at how to implement **Separation of Concerns** in a **.NET Core** application.

---

### **Scenario: Product Management System**

We'll design a **Product Management System** with a clear separation of concerns. In this system:

- The **Presentation Layer** will handle HTTP requests.
- The **Business Logic Layer** will manage the application rules.
- The **Data Access Layer** will interact with the database.

---

### **Step 1: Domain Model (Business Logic Layer)**

The **domain model** will represent the business concepts and should not know about data storage.

```csharp
// Domain Model (Product.cs)
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }

    // Business logic: Calculate discount price
    public decimal GetDiscountedPrice(decimal discountPercentage)
    {
        return Price - (Price * discountPercentage / 100);
    }
}
```

This class represents the **product domain** and contains **business logic**, but is not concerned with how data is stored or retrieved.

---

### **Step 2: Data Access Layer**

The **data access layer** will handle database interaction, such as fetching or saving data. Here, we’ll use **Entity Framework Core** to interact with the database.

```csharp
// Data Access Layer (ProductRepository.cs)
public class ProductRepository : IProductRepository
{
    private readonly ApplicationDbContext _context;

    public ProductRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<Product> GetByIdAsync(int id)
    {
        return await _context.Products.FindAsync(id);
    }

    public async Task<IEnumerable<Product>> GetAllAsync()
    {
        return await _context.Products.ToListAsync();
    }

    public async Task AddAsync(Product product)
    {
        await _context.Products.AddAsync(product);
        await _context.SaveChangesAsync();
    }

    public async Task UpdateAsync(Product product)
    {
        _context.Products.Update(product);
        await _context.SaveChangesAsync();
    }

    public async Task DeleteAsync(int id)
    {
        var product = await GetByIdAsync(id);
        if (product != null)
        {
            _context.Products.Remove(product);
            await _context.SaveChangesAsync();
        }
    }
}
```

Here, the **`ProductRepository`** only interacts with the **database**. It is solely responsible for **data access** and doesn’t deal with business logic.

---

### **Step 3: Service Layer**

The **service layer** provides a bridge between the **business logic** and **data access** layers. It’s where business operations are orchestrated and where complex logic is implemented.

```csharp
// Service Layer (ProductService.cs)
public class ProductService
{
    private readonly IProductRepository _productRepository;

    public ProductService(IProductRepository productRepository)
    {
        _productRepository = productRepository;
    }

    public async Task<Product> GetProductByIdAsync(int id)
    {
        return await _productRepository.GetByIdAsync(id);
    }

    public async Task AddProductAsync(Product product)
    {
        // You can add some business validation here before calling the repository.
        await _productRepository.AddAsync(product);
    }

    public async Task UpdateProductAsync(Product product)
    {
        // Business logic for updating a product can go here.
        await _productRepository.UpdateAsync(product);
    }

    public async Task DeleteProductAsync(int id)
    {
        await _productRepository.DeleteAsync(id);
    }
}
```

The **ProductService** orchestrates the flow of data from the **ProductRepository** and applies any necessary business rules. The **service layer** is the heart of your business logic and helps keep the data access logic separate.

---

### **Step 4: Presentation Layer**

The **presentation layer** will manage user interactions and display data. This could be a **Web API** (as in an MVC or RESTful architecture) or a **UI**. We’ll use **Controllers** in an API to handle HTTP requests.

```csharp
// Presentation Layer (ProductController.cs)
[ApiController]
[Route("api/[controller]")]
public class ProductController : ControllerBase
{
    private readonly ProductService _productService;

    public ProductController(ProductService productService)
    {
        _productService = productService;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetProduct(int id)
    {
        var product = await _productService.GetProductByIdAsync(id);
        if (product == null) return NotFound();
        return Ok(product);
    }

    [HttpPost]
    public async Task<IActionResult> CreateProduct([FromBody] Product product)
    {
        await _productService.AddProductAsync(product);
        return CreatedAtAction(nameof(GetProduct), new { id = product.Id }, product);
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> UpdateProduct(int id, [FromBody] Product product)
    {
        if (id != product.Id) return BadRequest();
        await _productService.UpdateProductAsync(product);
        return NoContent();
    }

    [HttpDelete("{id}")]
    public async Task<IActionResult> DeleteProduct(int id)
    {
        await _productService.DeleteProductAsync(id);
        return NoContent();
    }
}
```

The **ProductController** is the **presentation layer** that exposes the **ProductService** to the outside world via **HTTP API** endpoints. It handles **client requests** and responds accordingly.

---

### **Step 5: Dependency Injection**

In the **Startup.cs** file, we configure dependency injection to connect all the layers.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Register DbContext
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    // Register the repository and service layer
    services.AddScoped<IProductRepository, ProductRepository>();
    services.AddScoped<ProductService>();

    // Register API controllers
    services.AddControllers();
}
```

---

### **Benefits of Separation of Concerns (SoC)**

1. **Maintainability**: Each layer has a clear responsibility, making it easier to maintain and modify parts of the system.
2. **Testability**: It’s easier to write unit tests for individual layers (e.g., testing business logic in `ProductService` without worrying about database interaction).
3. **Scalability**: If you need to scale a specific part of your application, such as the business logic or database, you can do so independently without affecting other parts of the system.
4. **Flexibility**: You can easily change implementation details of one layer without impacting others. For example, you can switch from Entity Framework to another ORM or a NoSQL database without touching your business logic or presentation layers.

---

### **Conclusion**

By following the **Separation of Concerns (SoC)** principle in this **.NET Core** application, we’ve achieved a clean, modular design where each layer is focused on a specific responsibility. This makes the system more maintainable, scalable, and testable.

Would you like to see more advanced examples or implementations in specific layers?