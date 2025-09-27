
### **Model-View-Controller (MVC) in .NET Core**

The **Model-View-Controller (MVC)** architectural pattern is a widely used design pattern that separates an application into three main components: **Model**, **View**, and **Controller**. This separation helps manage complex applications, making them more modular, scalable, and maintainable.

- **Model**: Represents the data and the business logic of the application. It defines the structure and behavior of the data.
- **View**: Represents the presentation layer, displaying the data to the user and interacting with them.
- **Controller**: Acts as an intermediary between the Model and the View. It listens for user input, manipulates the data, and updates the view accordingly.

In a **.NET Core MVC application**, this pattern is implemented to create dynamic web applications with clear separation of concerns. Below are the steps to build an MVC application in .NET Core.

---

### **Steps to Create a .NET Core MVC Application**

---

### **1. Create the MVC Application**

#### **Step 1.1: Create a New .NET Core MVC Project**

1. Open the terminal or Visual Studio and create a new MVC project by running the following command in your terminal or using Visual Studio:
    
    ```bash
    dotnet new mvc -n MvcExample
    cd MvcExample
    ```
    
    This will generate a new **MVC** project structure with predefined folders like **Controllers**, **Views**, and **Models**.
    

#### **Step 1.2: Run the Application**

Run the application using:

```bash
dotnet run
```

The application will be available at `http://localhost:5000`.

---

### **2. Define the Model**

#### **Step 2.1: Create a Model**

In the **Models** folder, define the data structure that represents your application's data. For example, a `Product` model that represents a product in an online store:

```csharp
namespace MvcExample.Models
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

The `Product` model will be used to store product data and transfer it between the controller and the view.

---

### **3. Define the Controller**

#### **Step 3.1: Create a Controller**

Create a controller that will handle incoming HTTP requests and interact with the model. The controller will receive the user’s request, perform actions like retrieving or updating data, and return a view to display.

In the **Controllers** folder, create a `ProductController` class:

```csharp
using Microsoft.AspNetCore.Mvc;
using MvcExample.Models;
using System.Collections.Generic;

namespace MvcExample.Controllers
{
    public class ProductController : Controller
    {
        // Simulating an in-memory database of products
        private static List<Product> products = new List<Product>
        {
            new Product { Id = 1, Name = "Laptop", Price = 1500.00m },
            new Product { Id = 2, Name = "Smartphone", Price = 700.00m },
            new Product { Id = 3, Name = "Tablet", Price = 400.00m }
        };

        // GET: /Product/
        public IActionResult Index()
        {
            return View(products);
        }

        // GET: /Product/Details/5
        public IActionResult Details(int id)
        {
            var product = products.Find(p => p.Id == id);
            if (product == null)
            {
                return NotFound();
            }
            return View(product);
        }
    }
}
```

- The **Index** action retrieves a list of products and passes it to the view.
- The **Details** action fetches a specific product by its ID and passes it to the view.

---

### **4. Create the View**

#### **Step 4.1: Create Views**

In the **Views** folder, create the views that will render the data to the user. Views are responsible for displaying the data passed from the controller.

- **Views/Product/Index.cshtml** (List of products)

```html
@model IEnumerable<MvcExample.Models.Product>

<h2>Product List</h2>

<ul>
    @foreach (var product in Model)
    {
        <li>
            <a href="@Url.Action("Details", "Product", new { id = product.Id })">
                @product.Name - @product.Price.ToString("C")
            </a>
        </li>
    }
</ul>
```

This view displays the list of products and links to the **Details** page for each product.

- **Views/Product/Details.cshtml** (Product details)

```html
@model MvcExample.Models.Product

<h2>Product Details</h2>

<div>
    <h3>@Model.Name</h3>
    <p>Price: @Model.Price.ToString("C")</p>
</div>
```

This view displays detailed information for a specific product.

---

### **5. Configure the Routing**

In **Startup.cs**, ensure that the routing is configured correctly to map URLs to the appropriate controller and action. This is typically configured by default in a new MVC project.

In the `Configure` method, ensure that **MVC routing** is enabled:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

This configuration sets the default route to the **HomeController**’s **Index** action, but since we are using **ProductController**, it will map accordingly.

---

### **6. Run the Application**

Now that the application is set up, you can test it.

2. Run the application:
    
    ```bash
    dotnet run
    ```
    
3. Navigate to `http://localhost:5000/Product/` to see the list of products.
    
4. Click on a product to view its details at `http://localhost:5000/Product/Details/{id}`.
    

---

### **7. Optional: Add Data Persistence**

To persist the product data, you can integrate a database like **Entity Framework Core** or any other ORM.

For example, you can use **SQLite**, **SQL Server**, or **PostgreSQL** to store and retrieve product information.

5. Add the necessary NuGet packages for **Entity Framework Core**:
    
    ```bash
    dotnet add package Microsoft.EntityFrameworkCore.Sqlite
    ```
    
6. Create a `DbContext` class to represent your database.
    
7. Add the data initialization logic in the `Startup.cs` file or a service class to seed the database.
    

---

### **Summary**

In this example, we created a simple **.NET Core MVC** application with a **Model-View-Controller (MVC)** architecture.

- **Model**: Represents data structures (e.g., `Product`).
- **View**: Displays data to the user using Razor views.
- **Controller**: Handles requests, manipulates data, and selects the view to return.

This architecture helps in keeping the concerns of the application separate, making it easier to maintain and scale.