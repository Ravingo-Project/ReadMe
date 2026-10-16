# AI Usage and Learning Notes

## 1. Tools Used and How They Were Used
* **Large Language Models (ChatGPT / Claude / GitHub Copilot)**:
  * Used to generate standard boilerplate code and establish a clean **3-Tier Layered Architecture** (Entities, EF Core DbContext, DTOs, Service Layer, and API Controllers)[cite: 1, 2].
  * Used to scaffold initial seed data in `DbInitializer.cs` for 3 branches (Colombo, Kandy, Galle), 5 grocery products, and starting stock distributions[cite: 1, 2].
  * Assisted in structuring automated unit tests using **xUnit** and **FluentAssertions** to validate transfer validation rules (such as insufficient stock rejection and successful stock deduction)[cite: 1, 2].
  * Assisted in scaffolding the React (Vite) single-page client to interact with backend endpoints and handle real-time feedback[cite: 1].

---

## 2. Where the AI Was Inaccurate or Confusing, and How It Was Resolved
* **EF Core In-Memory Database and Relational Transactions (`BeginTransactionAsync`)**:
  * **The Issue**: When generating automated unit tests using `Microsoft.EntityFrameworkCore.InMemory`, the test suite failed at runtime with the following error:  
    `The configured execution strategy does not support user-initiated transactions.`[cite: 2]
  * **Root Cause**: The AI initially used relational database transaction logic (`await _context.Database.BeginTransactionAsync()`) inside `TransferService.cs`[cite: 2]. Because EF Core's In-Memory provider does not support relational transactions, it threw an exception during unit testing[cite: 2].
  * **Detection & Fix**: The issue was detected immediately when running tests via the Visual Studio Test Explorer. It was resolved by configuring the In-Memory DbContext options to suppress transaction warnings using:  
    `.ConfigureWarnings(w => w.Ignore(InMemoryEventId.TransactionIgnoredWarning))`  
    This allowed the business logic to execute atomic transactions properly against SQLite in production while allowing tests to pass in-memory[cite: 2].

* **OpenAPI vs. Swagger UI Configuration in .NET 9/10**:
  * **The Issue**: The AI initially suggested registering Swagger using the classic .NET 6/7/8 method (`app.UseSwagger()` and `app.UseSwaggerUI()`)[cite: 2]. However, the modern .NET 9 Web API template uses built-in OpenAPI mapping (`builder.Services.AddOpenApi()` and `app.MapOpenApi()`), causing the `/swagger/index.html` URL to return a 404 Not Found[cite: 2].
  * **Detection & Fix**: Noticed upon launching the application when the interactive documentation page failed to load[cite: 2]. Resolved by installing the `Swashbuckle.AspNetCore` package explicitly and configuring the middleware correctly in `Program.cs`[cite: 2].

---

## 3. What Was New to Me and How I Learned It
* **Automated Unit Testing with xUnit and EF Core**:
  * Writing automated unit tests for business validation rules in .NET was a new area[cite: 2].
  * Learned how to use **xUnit** alongside **FluentAssertions** to assert edge cases (e.g., verifying that transfers are rejected when stock is insufficient and ensuring rollbacks keep inventory unchanged)[cite: 2].
  * Learned how to isolate unit tests from production databases by leveraging EF Core's In-Memory database provider[cite: 2].
* **EF Core with SQLite and the Code-First Workflow**:
  * Previously familiar with setting up standalone database servers manually (e.g., via MySQL Workbench).
  * Learned how to configure a lightweight, file-based SQLite database (`mihira.db`) directly inside .NET using EF Core and automated schema generation via `context.Database.EnsureCreated()` and automated seed scripts[cite: 1, 2].
