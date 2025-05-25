# .NET Development Guidelines: dotnet.md

## 1. Guiding Principles

- MICROSOFT'S FRAMEWORK DESIGN GUIDELINES: Adhere to the official Framework Design Guidelines for consistency and best practices in API design and library development.
- SOLID PRINCIPLES: Design software using SOLID (Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion) principles to create maintainable, scalable, and robust applications.
- ASYNCHRONOUS PROGRAMMING: Embrace `async` and `await` for I/O-bound and CPU-bound operations to improve responsiveness and scalability (see Section 4.2 for details).
- LINQ FOR DATA MANIPULATION: Utilize Language-Integrated Query (LINQ) for expressive and readable data access and manipulation (see Section 4.3 for details).
- FAVOR ABSTRACTIONS AND DECLARATIVE APPROACHES:
  - HIGH-LEVEL CONSTRUCTS: Prioritize built-in high-level abstractions and constructs (e.g., Task Parallel Library, expression-bodied members, pattern matching) provided by C# and .NET over explicit, low-level imperative code, complementing specific principles like LINQ and asynchronous programming.
  - DECLARATIVE STYLE: Strive for a declarative programming style where appropriate, focusing on *what* needs to be achieved rather than detailing *how* to achieve it step-by-step. This often leads to more readable and maintainable code.
  - ABSTRACTION LAYERS: Design with clear abstraction layers (interfaces, abstract classes, services) to decouple components, improve testability, and manage complexity.
  - FUNCTIONAL CONCEPTS: Consider incorporating functional programming concepts (e.g., immutability, pure functions, higher-order functions, expression-based logic) where they enhance clarity, predictability, and reduce side effects.
- VERTICAL SLICE ARCHITECTURE (VSA) WITH REPR PATTERN:
  - PREFERENCE: Favor Vertical Slice Architecture, organizing code by feature, over traditional layered (e.g., MVC) approaches for better cohesion and maintainability.
  - REPR (REQUEST-ENDPOINT-RESPONSE): Within each slice, utilize the REPR pattern (or Request-Handler-Response). This involves:
    - A `Request` object encapsulating input for an operation.
    - An `Endpoint` or `Handler` containing the logic for that specific operation.
    - A `Response` object for the operation's result.
  - BENEFITS: This combination promotes high cohesion by co-locating feature-specific code, low coupling between features, improved testability, and aligns well with SOLID principles. It simplifies understanding, modification, and scaling of features.
  - TOOLING: Leverages modern .NET features like Minimal APIs and libraries such as MediatR for clean implementation.

## 2. Project Setup & Dependency Management

### 2.1. Solution and Project Structure (`.sln`, `.csproj`)
- SOLUTION FILES (`.sln`): Organize related projects within a single solution file.
- PROJECT FILES (`.csproj`): Use the SDK-style project format for cleaner and more manageable project files. Define target frameworks, dependencies, and build configurations here.
- LOGICAL GROUPING: Structure projects logically by feature, layer (e.g., API, Core, Infrastructure, Domain), or type (e.g., Class Library, Web API, Console App).

### 2.2. Dependency Management: NuGet
- PRIMARY TOOL: NuGet is the package manager for .NET. Use it to add, update, and manage third-party libraries and tools.
- PACKAGE REFERENCES: Manage dependencies via `<PackageReference>` items in `.csproj` files.
  ```xml
  <!-- .csproj example -->
  <ItemGroup>
    <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
    <PackageReference Include="Microsoft.Extensions.Logging" Version="8.0.0" />
  </ItemGroup>
  ```
- `DOTNET CLI` FOR PACKAGE MANAGEMENT:
  ```bash
  # Add a package
  dotnet add package Newtonsoft.Json

  # List outdated packages
  dotnet list package --outdated

  # Update a package
  dotnet add package Microsoft.Extensions.Logging --version 8.0.0
  ```
- PRIVATE FEEDS: Configure and use private NuGet feeds for internal libraries.
- LOCK FILES (CENTRAL PACKAGE MANAGEMENT): For larger solutions, consider Central Package Management (CPM) with a `Directory.Packages.props` file and `ManagePackageVersionsCentrally` set to `true` to manage dependency versions consistently across projects. Use `dotnet restore --use-lock-file` to generate a `packages.lock.json` file for reproducible builds.

### 2.3. `dotnet CLI`
- CORE TOOL: The `dotnet` command-line interface (CLI) is essential for creating, building, testing, and publishing .NET applications.
  ```bash
  # Create a new console application
  dotnet new console -n MyConsoleApp

  # Build a project
  dotnet build

  # Run a project
  dotnet run --project MyProject/MyProject.csproj

  # Publish an application
  dotnet publish -c Release -o ./publish_output
  ```

## 3. Code Style, Formatting & Linting

### 3.1. C# Coding Conventions
- MICROSOFT'S CONVENTIONS: Follow Microsoft's official C# Coding Conventions for naming, layout, and commenting.
  - Use PascalCase for classes, records, enums, methods, properties, and events.
  - Use camelCase for local variables and method parameters.
  - Prefix interfaces with `I` (e.g., `IUserService`).

### 3.2. Linters & Analyzers
- .NET ANALYZERS (ROSLYN ANALYZERS): Leverage built-in and third-party .NET analyzers to identify potential code issues, style violations, and anti-patterns during development and build.
- STYLECOP.ANALYZERS: Use StyleCop.Analyzers NuGet package for enforcing C# style and consistency rules.
- FXCOP ANALYZERS / .NET CODE QUALITY ANALYZERS: Use these to enforce design, globalization, interoperability, maintainability, naming, performance, reliability, security, and usage rules.

## 4. Idiomatic C# & Abstractions

The following C# language features and patterns are key to writing idiomatic, abstract, and declarative code, aligning with the guiding principles:

### 4.1. Language Features
- PROPERTIES: Use properties for exposing data instead of public fields.
- EXPRESSION-BODIED MEMBERS: Use for concise implementations of simple methods, properties, constructors, etc.
- PATTERN MATCHING: Utilize pattern matching for more readable and powerful conditional logic and type checking.
- RECORDS (`record`): Use records for immutable data transfer objects (DTOs) and types where value-based equality is desired.
- TUPLES: Use tuples for returning multiple values from a method without creating a dedicated type.
- NULLABLE REFERENCE TYPES: Enable and use nullable reference types (`#nullable enable`) to help prevent `NullReferenceException` errors.

### 4.2. Asynchronous Programming (`async`/`await`)
- BEST PRACTICES:
  - Use `async` all the way up the call stack.
  - Use `ConfigureAwait(false)` in library code to avoid deadlocks when not needing to resume on the original context.
  - Avoid `async void` except for event handlers.
  - Use `CancellationToken` for cancellable operations.

### 4.3. LINQ (Language-Integrated Query)
- STRONG PREFERENCE FOR LINQ OVER RAW SQL: For data access, especially when interacting with databases via an Object-Relational Mapper (ORM) like Entity Framework Core, LINQ should be the primary choice over constructing raw SQL queries as strings.
- KEY BENEFITS OF LINQ:
  - READABILITY & MAINTAINABILITY: LINQ queries are written in C#, integrating naturally with the application's language. This improves clarity, makes them easier to refactor, and allows developers to leverage familiar language constructs for complex query logic.
    ```csharp
    // Example: Readable LINQ query
    var recentHighValueOrders = dbContext.Orders
        .Where(o => o.OrderDate > DateTime.UtcNow.AddMonths(-1) && o.TotalAmount > 1000)
        .Include(o => o.Customer)
        .OrderByDescending(o => o.OrderDate)
        .Select(o => new { o.OrderId, CustomerName = o.Customer.Name, o.TotalAmount })
        .ToList();
    ```
  - TYPE SAFETY: Queries are checked at compile-time. Errors due to incorrect property names, type mismatches, or invalid operations are caught early, significantly reducing runtime bugs.
  - SECURITY (SQL INJECTION PREVENTION): When used with ORMs like EF Core, LINQ queries are typically parameterized automatically or constructed in a way that inherently protects against SQL injection attacks. This is a major security advantage over manually constructed SQL strings.
  - INTELLISENSE AND DEBUGGING: Developers benefit from rich IntelliSense, autocompletion, and robust debugging capabilities directly within the C# environment when writing LINQ queries.
  - ABSTRACTION: LINQ providers can abstract underlying database-specific SQL syntax, potentially simplifying database migrations or supporting multiple database backends (though complex queries might still require adjustments).
  - DEFERRED EXECUTION: Understand and leverage LINQ's deferred execution model for building dynamic queries and optimizing performance by fetching data only when needed.
- COMPARISON WITH RAW SQL QUERIES:
  - EASE OF USE: LINQ is generally more intuitive for C# developers. Raw SQL requires direct SQL language proficiency.
  - SAFETY: LINQ is inherently safer regarding SQL injection. Raw SQL requires meticulous manual parameterization to be secure.
    - RAW SQL (LESS SAFE WITHOUT CARE): `var query = "SELECT * FROM Products WHERE Name = '" + productName + "'"; // Vulnerable!`
    - LINQ (SAFER): `var products = dbContext.Products.Where(p => p.Name == productName);`
  - PERFORMANCE:
    - For most common operations, EF Core translates LINQ queries into efficient parameterized SQL.
    - In highly complex scenarios or when needing to use database features not directly supported by LINQ, hand-tuned raw SQL *can* be more performant. However, this should be verified by profiling.
    - EF Core provides mechanisms like `FromSqlRaw` or `ExecuteSqlRawAsync` to execute raw SQL when necessary, but these still require careful handling of parameters for security.
- WHEN TO CONSIDER RAW SQL (SAFELY):
  - COMPLEX QUERIES: When a query is extremely complex and difficult or inefficient to express in LINQ.
  - DATABASE-SPECIFIC FEATURES: To leverage specific functions, hints, or features of a particular database system not exposed through the LINQ provider.
  - BULK OPERATIONS/PERFORMANCE TUNING: For scenarios where EF Core's change tracking or query generation might introduce overhead, and a highly optimized raw SQL query (e.g., for bulk updates/deletes not well-supported by older EF Core versions) is demonstrably faster after profiling.
  - ALWAYS USE PARAMETERIZATION: When raw SQL is used, it is CRITICAL to use parameterized queries to prevent SQL injection. Do NOT use string concatenation with user-supplied values.
    ```csharp
    // Safe Raw SQL with EF Core and Parameterization
    var searchTerm = ".NET Core";
    var blogs = dbContext.Blogs
        .FromSqlInterpolated($"SELECT * FROM Blogs WHERE Title LIKE {"%" + searchTerm + "%"}")
        .ToList();
    ```

### 4.4. Delegates and Events
- Use delegates and events for implementing the publish-subscribe pattern and decoupling components.
- Prefer `EventHandler<TEventArgs>` for standard event patterns.

### 4.5. Generics
- Use generics to create reusable, type-safe classes, interfaces, and methods.

### 4.6. Interfaces and Abstract Classes
- INTERFACES: Define contracts for capabilities. Prefer interfaces over base classes for polymorphism when multiple inheritance of behavior is not needed.
- ABSTRACT CLASSES: Provide common base functionality and allow derived classes to fill in specific details.

## 5. Error Handling & Logging

### 5.1. Exception Handling
- `TRY-CATCH-FINALLY`: Use structured exception handling.
- SPECIFIC EXCEPTIONS: Catch specific exceptions rather than generic `System.Exception`.
- CUSTOM EXCEPTIONS: Define custom exceptions for application-specific error conditions.
- AVOID SWALLOWING EXCEPTIONS: Do not catch exceptions without logging them or re-throwing appropriately.
- FAIL FAST: Let unrecoverable exceptions propagate to be handled at a higher level or terminate the application.

### 5.2. Logging
- `MICROSOFT.EXTENSIONS.LOGGING`: Use the `ILogger` abstraction provided by `Microsoft.Extensions.Logging` for consistent logging across applications.
- LOGGING PROVIDERS: Configure logging providers (e.g., Console, Debug, Serilog, NLog, Application Insights) based on the environment and requirements.
- STRUCTURED LOGGING: Prefer structured logging for easier querying and analysis of log data.
  ```csharp
  public class MyService
  {
      private readonly ILogger<MyService> _logger;

      public MyService(ILogger<MyService> logger)
      {
          _logger = logger;
      }

      public void ProcessOrder(int orderId)
      {
          _logger.LogInformation("Processing order {OrderId}", orderId);
          try
          {
              // ... processing logic ...
              _logger.LogInformation("Order {OrderId} processed successfully", orderId);
          }
          catch (Exception ex)
          {
              _logger.LogError(ex, "Error processing order {OrderId}", orderId);
              throw; // Re-throw or handle
          }
      }
  }
  ```

## 6. Performance Optimization

### 6.1. Common Optimization Techniques
- `SPAN<T>` AND `MEMORY<T>`: Use `Span<T>` and `Memory<T>` for high-performance, allocation-free operations on contiguous memory regions.
- AVOID EXCESSIVE ALLOCATIONS: Be mindful of object allocations in performance-critical paths (hot paths) to reduce GC pressure.
- CACHING: Implement caching strategies (in-memory, distributed) for frequently accessed or expensive-to-compute data.
- LINQ PERFORMANCE: Be aware of LINQ's deferred execution and potential for multiple enumerations. Use `.ToList()` or `.ToArray()` judiciously to materialize results when needed.
- GARBAGE COLLECTION (GC): Understand basic GC concepts. Avoid finalizers unless necessary.
- ENTITY FRAMEWORK CORE PERFORMANCE: Optimize EF Core queries (e.g., use `AsNoTracking()` for read-only queries, select only necessary columns, avoid N+1 problems).
