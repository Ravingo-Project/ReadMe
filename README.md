# .github


MIHIRA STORES - INTER-BRANCH STOCK TRANSFER MANAGEMENT SYSTEM
================================================================================

A lightweight, robust full-stack solution built for Mihira Stores to track and
execute stock movements between branches (Colombo, Kandy, Galle) reliably without
discrepancies.

This organization hosts the decoupled system in two dedicated repositories:
1. Backend  : ASP.NET Core Web API (.NET 9) with EF Core & SQLite
2. Frontend : Single-Page Dashboard built with React & Vite


--------------------------------------------------------------------------------
1. REPOSITORIES OVERVIEW
--------------------------------------------------------------------------------

* Backend Repository  : Contains the .NET 9 Web API, SQLite database configuration,
                        business rule validations, ACID transactions, and unit tests.
* Frontend Repository : Contains the React (Vite) single-page client, state
                        management, user feedback alerts, and transfer tables.


--------------------------------------------------------------------------------
2. PREREQUISITES
--------------------------------------------------------------------------------

Ensure you have the following installed on your machine:
* .NET SDK (version 10 ) Run on visual studio 2026
* Node.js (version 18.x or later) and npm
* Git




--------------------------------------------------------------------------------
3. LOCAL SETUP AND RUNNING INSTRUCTIONS
--------------------------------------------------------------------------------

Step 1: Clone Both Repositories
-------------------------------
Create a project workspace folder on your machine and clone both repositories:

    mkdir MihiraStores-Project
    cd MihiraStores-Project

    # Clone the Backend repository
    git clone `<BACKEND_REPOSITORY_URL>`

    # Clone the Frontend repository
    git clone `<FRONTEND_REPOSITORY_URL>` 


Step 2: Start the Backend API (.NET 10)
--------------------------------------
The backend uses SQLite with Entity Framework Core. On startup, it automatically
generates the database schema (mihira.db) and seeds initial branches, products,
and stocks.

1. Navigate to the backend directory:
    cd Backend

2. Restore NuGet dependencies:
    dotnet restore

3. Run the API:
    dotnet run

   make sure to run the RavingoTest lefthand side of the run button (green triangle button) 


5. The server runs at:
    API Endpoint          : https://localhost:7102 (or http://localhost:5194)
    Swagger Documentation : https://localhost:7102/swagger/index.html

(Keep this terminal open and running.)


Step 3: Start the Frontend Client (React + Vite)
------------------------------------------------
Open a NEW terminal window:

1. Navigate to the frontend directory:
    cd Frontend

2. Install dependencies:
    npm install

3. Environment Configuration:
   Create a .env file in the root of the Frontend repository:
    VITE_API_BASE_URL=https://localhost:7102

4. Launch the development server:
    npm run dev

5. Access the application in your browser:
    http://localhost:5173


--------------------------------------------------------------------------------
4. RUNNING AUTOMATED TESTS
--------------------------------------------------------------------------------

To run the automated xUnit tests validating the core business rules:

1. In the terminal, navigate to the Backend repository folder:
    cd Backend

2. Execute the test runner:
    dotnet test

or make sure to run the MIhiraStores.Test lefthand side of the run button (green triangle button) 
--------------------------------------------------------------------------------
5. BUSINESS RULES ENFORCED
--------------------------------------------------------------------------------

Every stock transfer (POST /transfers) is executed inside an atomic transaction
and strictly enforces:
1. Quantity Check: Must be greater than zero (<= 0 rejected).
2. Distinct Branches: Source and destination branches cannot be the same.
3. Existence Check: Both branch IDs and the product ID must exist.
4. Sufficiency: The sending branch must possess sufficient stock.
5. Transactional Atomicity (ACID): If any check fails, the transaction rolls
   back immediately, leaving all stock levels completely unchanged.


--------------------------------------------------------------------------------
6. ARCHITECTURAL ASSUMPTIONS
--------------------------------------------------------------------------------

* Architecture Choice: A clean 3-Tier Layered Architecture was implemented
  (Controllers -> Services -> Data Access) instead of an over-engineered Clean
  Architecture to balance delivery speed and code maintainability within the 3-hour limit.
* EF Core as Unit of Work: Avoided a redundant generic repository layer because
  EF Core's DbContext and DbSet natively implement the Unit of Work and
  Repository patterns, providing direct atomic transaction rollback support.
* Decoupled Repositories: Separate repositories were maintained for Backend and
  Frontend to allow independent versioning and modular CI/CD pipelines.
* Stateless Authentication: User authentication was omitted as per assessment scope.


--------------------------------------------------------------------------------
7. FUTURE ENHANCEMENTS (WITH MORE TIME)
--------------------------------------------------------------------------------

* Optimistic Concurrency Control: Add row-version concurrency tokens to prevent
  race conditions during simultaneous multi-user transfers.
* Idempotency Keys: Support an Idempotency-Key header on POST /transfers to
  prevent duplicate submissions over unstable connections.
* Audit & Reversal Workflows: Add administrative endpoints allowing authorized
  personnel to cancel or rollback erroneous transfers with complete audit trails.
* Containerization: Add a docker-compose setup to orchestrate both services
  with a single command.
================================================================================
