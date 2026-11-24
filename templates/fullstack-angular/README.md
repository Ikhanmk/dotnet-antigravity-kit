# Full-Stack Angular & .NET 10 Starter Template

This template provides a production-grade full-stack architecture combining:
- **Backend**: ASP.NET Core (.NET 10) Minimal API using **Vertical Slice Architecture**, EF Core, and Scalar documentation.
- **Frontend**: Standalone **Angular 19+** application with Signals, OnPush change detection, and typed API services.

## Architecture

```text
MyFullStackApp/
├── src/
│   ├── Backend/                 # ASP.NET Core Web API
│   │   ├── Features/            # Vertical slices (Orders, Customers, etc.)
│   │   │   └── Customers/
│   │   │       ├── CreateCustomer.cs
│   │   │       └── GetCustomers.cs
│   │   ├── Infrastructure/      # EF Core DbContext & Migrations
│   │   └── Program.cs           # Minimal API registration & Scalar docs
│   │
│   └── Frontend/                # Angular Standalone SPA
│       └── src/app/
│           ├── core/            # Functional interceptors & base services
│           ├── features/        # Feature components matching backend slices
│           │   └── customers/
│           │       ├── customer.service.ts
│           │       └── customer-list.component.ts
│           └── app.config.ts    # provideHttpClient, provideRouter
```

## Running the Application
1. **Backend**:
   ```bash
   cd src/Backend
   dotnet run
   ```
   API will be available at `https://localhost:5001/scalar/v1`.

2. **Frontend**:
   ```bash
   cd src/Frontend
   npm install
   npm start
   ```
   Frontend will be available at `http://localhost:4200` with proxy configured to route `/api/*` to `https://localhost:5001`.
