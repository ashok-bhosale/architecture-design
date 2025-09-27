### **.NET Core Application with Persistence Ignorance**

**Persistence Ignorance (PI)** is a design principle that promotes the **separation of domain logic** from the **data storage** logic. The main idea is that domain entities should not be aware of how their data is stored or retrieved. This principle helps to maintain **clean domain models** and **flexibility** in how data is persisted, allowing the system to change databases or storage mechanisms without affecting the domain model.

In .NET Core, this can be achieved by keeping the domain models free from any direct dependencies on the **ORM (Object-Relational Mapper)**, such as Entity Framework, or the database itself. Instead, you should use **repositories** or **services** to handle data access, and your domain models should focus on business logic.

---

### **📌 Key Features of Persistence Ignorance:**

1. **Domain Model Independence**: The domain model should not depend on the database layer or data persistence mechanisms.
2. **Decoupling**: The system is decoupled from the underlying database technology.
3. **Testability**: With this approach, the domain logic becomes easier to test without needing access to a real database.
4. **Flexibility**: You can change the database implementation (e.g., switch from SQL Server to MongoDB) without modifying the domain models.

---

### **🛠 Scenario: A Simple Application for Managing Products**

Let’s design a simple **Product Management System** where we will keep the **Persistence Ignorance** principle intact.

---

#### **1. Define the Domain Model (Product)**

The domain model focuses only on business logic and should not contain any persistence-related code (such as `DbContext` or `Entity Framework` dependencies).

```csharp
// Domain Model (Product.cs)
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }

    // Business logic: e.g., discount application
    public decimal ApplyDiscount(decimal discountPercentage)
    {
        if (discountPercentage < 0 || discountPercentage > 100)
            throw new ArgumentException("Discount percentage must be between 0 and 100.");
        
        return Price * (1 - discountPercentage / 100);
    }
}
```

This `Product` model focuses only on **business rules** like applying a discount and does **not** have any knowledge of how it will be persisted or retrieved from the database.

---

#### **2. Create a Repository Interface**

The repository interface is used to interact with the data layer. This abstraction hides the actual database details from the domain model.

```csharp
// Repository Interface (IProductRepository.cs)
public interface IProductRepository
{
    Task<Product> GetByIdAsync(int id);
    Task<IEnumerable<Product>> GetAllAsync();
    Task AddAsync(Product product);
    Task UpdateAsync(Product product);
    Task DeleteAsync(int id);
}
```

This interface defines methods for **CRUD operations** but does not expose any database implementation details.

---

#### **3. Implement the Repository Using Entity Framework (or any other persistence mechanism)**

Now, we implement the `IProductRepository` interface using **Entity Framework**. This implementation is responsible for **interacting with the database**, but the domain model (Product) is unaware of it.

```csharp
// EF Implementation (ProductRepository.cs)
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

This repository contains **EF Core logic** to interact with the database, but the `Product` model remains unaware of the persistence details.

---

#### **4. The Application Layer (Service Layer)**

The service layer coordinates the domain logic and interacts with the repository, providing an abstraction for the **business logic**.

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
        // Additional business logic can be applied here before saving
        await _productRepository.AddAsync(product);
    }

    public async Task UpdateProductAsync(Product product)
    {
        // Additional business logic can be applied here before updating
        await _productRepository.UpdateAsync(product);
    }

    public async Task DeleteProductAsync(int id)
    {
        await _productRepository.DeleteAsync(id);
    }
}
```

The `ProductService` interacts with the `IProductRepository` to perform business operations without knowing about the data layer. The service layer is also **independent of the database**.

---

#### **5. Application Setup**

In the `Startup.cs`, you can set up the dependency injection for the repository and service:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Register DbContext and repository
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddScoped<IProductRepository, ProductRepository>();
    services.AddScoped<ProductService>();
}
```

Now, the application is **prepared for Dependency Injection**, and the **Persistence Ignorance** principle is maintained, where the domain model (Product) has no awareness of the database or how the data is stored.

---

### **🎯 Benefits of Persistence Ignorance:**

1. **Separation of Concerns**: The domain model is only responsible for **business logic**, without worrying about data storage or retrieval mechanisms.
2. **Flexibility**: You can easily change the **persistence layer** (e.g., from SQL to NoSQL or even in-memory storage) without affecting the core domain logic.
3. **Testability**: Testing the domain logic becomes easier because the domain model is free of database dependencies. You can mock repositories or services to isolate the logic.
4. **Maintainability**: Since the domain and persistence concerns are **separated**, the code becomes easier to maintain and extend.

---

### **🚀 Conclusion**

By following the **Persistence Ignorance** principle in this example, we ensured that the **Product domain model** is **decoupled from the database layer**. The **repository pattern** provides a flexible way to interact with the data, while the **domain logic** is unaffected by changes in the persistence layer. This approach promotes cleaner code and makes your application **more scalable**, **testable**, and **maintainable**.

Would you like more details on implementing **Persistence Ignorance** in specific use cases?