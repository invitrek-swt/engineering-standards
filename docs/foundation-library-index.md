# Foundation Library Index — Invitrek-swt

**Applies to:** All domain projects (DeveloperStudio, Html5Designer, Nexus, and future domains)  
**Last updated:** See git history

> **Policy:** Every domain project must depend on Foundation (and UIFramework where applicable) rather than re-implementing cross-cutting or UI concerns. See `architecture-guidelines.md §11` for the full platform library policy.

---

## Solution Structure Note

Foundation is split into two independent sub-families that live under the same `Foundation/Main` solution but are kept strictly separate:

| Sub-family | Folder | What it contains |
|------------|--------|------------------|
| **Foundation (Non-UI)** | `src/Foundation/` | Pure class libraries — cross-cutting concerns, data access, service model, CLI, CodeModel. No WPF/WinForms/UI dependencies. Targets `net48` + `net10.0`. |
| **UIFramework** | `src/UIFramework/` | All reusable UI controls and MVVM infrastructure. Currently WPF-first (WPF controls, MVVM base classes, docking, dialogs, theming). Platform abstractions are in place for future Android, iOS, and ASP.NET Core Blazor/Razor. Targets `net48` + `net10.0-windows` (desktop-bound targets today). |

Both sub-families will be published as separate NuGet packages post-MVP so that non-UI server-side projects can depend on Foundation without pulling in any UI assemblies.

---

## Quick Reference

### Foundation (Non-UI) Libraries

| Package | Purpose | Required by |
|---------|---------|-------------|
| [`Invitrek.Foundation.Core`](#1-invitrekfoundationcore) |
| [`Invitrek.Foundation.Data`](#2-invitrekfoundationdata) | Repository framework — ORM-free data access, entity base types, CQRS repository pattern, DB context abstraction | All projects with DB access |
| [`Invitrek.Foundation.Data.SqlServer`](#3-invitrekfoundationdatasqlserver) | SQL Server provider for `Foundation.Data` | SQL Server projects |
| [`Invitrek.Foundation.Data.PostgreSql`](#4-invitrekfoundationdatapostgresql) | PostgreSQL provider for `Foundation.Data` | PostgreSQL projects |
| [`Invitrek.Foundation.Data.MySql`](#5-invitrekfoundationdatamysql) | MySQL/MariaDB provider for `Foundation.Data` | MySQL/MariaDB projects |
| [`Invitrek.Foundation.Data.Sqlite`](#6-invitrekfoundationdatasqlite) | SQLite provider for `Foundation.Data` | SQLite / embedded projects |
| [`Invitrek.Foundation.Data.Oracle`](#7-invitrekfoundationdataoracle) | Oracle provider for `Foundation.Data` | Oracle projects |
| [`Invitrek.Foundation.ServiceModel`](#8-invitrekfoundationservicemodel) | WCF/CoreWCF service base classes, channel management, endpoint configuration | All WCF / multi-protocol services |
| [`Invitrek.Foundation.ServiceModel.Hosting`](#9-invitrekfoundationservicemodelhosting) | .NET Framework 4.8 `ServiceHost`-based hosting bootstrap | `.net48` WCF hosts |
| [`Invitrek.Foundation.ServiceModel.AspNetCore`](#10-invitrekfoundationservicemodelaspnetcore) | .NET 10 ASP.NET Core / CoreWCF hosting bootstrap | `.net10` WCF / REST hosts |
| [`Invitrek.Foundation.ServiceModel.Proxy`](#11-invitrekfoundationservicemodelproxy) | Client-side proxy base classes and channel factory abstractions | All service consumers |
| [`Invitrek.Foundation.Messaging.RabbitMQ`](#12-invitrekfoundationmessagingrabbitmq) | RabbitMQ provider for `IMessagePublisher` / `IMessageSubscriber` | Message-queue projects |
| [`Invitrek.Foundation.CodeModel`](#13-invitrekfoundationcodemodel) | Fluent C# code-generation API — strongly typed builders for classes, properties, methods, namespaces | DevTools, code generators |
| [`Invitrek.Foundation.Caching.Memory`](#14-invitrekfoundationcachingmemory) | In-process memory cache provider | All in-proc caching |
| [`Invitrek.Foundation.Caching.Redis`](#15-invitrekfoundationcachingredis) | Redis distributed cache provider | Distributed / multi-instance caching |
| [`Invitrek.Foundation.Logging.NLog`](#16-invitrekfoundationloggingnlog) | NLog sink for `Foundation.Core` `Logger` | Projects requiring NLog |
| [`Invitrek.Foundation.Logging.Serilog`](#17-invitrekfoundationloggingserilog) | Serilog sink for `Foundation.Core` `Logger` | Projects requiring Serilog |
| [`Invitrek.Foundation.Telemetry`](#18-invitrekfoundationtelemetry) | Application telemetry / metrics abstractions | All production services |
| [`Invitrek.Foundation.UIAutomation`](#19-invitrekfoundationuiautomation) | UI test automation abstractions (desktop, web, mobile) | Test projects |
| [`Invitrek.Foundation.Workflow`](#20-invitrekfoundationworkflow) | Workflow engine abstractions | Workflow-enabled projects |
| [`Invitrek.Foundation.AI`](#21-invitrekfoundationai) | AI integration abstractions (planned) | AI-enabled projects |
| [`Invitrek.Foundation.Security`](#22-invitrekfoundationsecurity) | Authentication, password hashing, encryption, membership | Identity / auth projects |

### UIFramework Libraries

| Package | Purpose | Required by |
|---------|---------|-------------|
| [`Invitrek.Foundation.UI`](#23-invitrekfoundationui) | Core UI abstractions — dialog services, navigation, command infrastructure, data bar, editor contracts. Base for all platform UI. | All desktop/UI projects |
| [`Invitrek.Foundation.Mvvm`](#24-invitrekfoundationmvvm) | MVVM kernel — `ViewModelBase`, `IViewModel`, `RelayCommand`, `ObservableObject`, command factory | All MVVM projects |
| [`Invitrek.Foundation.Mvvm.Extensions`](#25-invitrekfoundationmvvmextensions) | Domain-level MVVM base classes — `EntityListViewModel<T>`, `EntityEditViewModel<T>`, `PagedEntityListViewModel<T>`, `EditViewModel` | All entity CRUD views |
| [`Invitrek.Foundation.Presentation.Controls`](#26-invitrekfoundationpresentationcontrols) | WinUI3-equivalent WPF control suite — NavigationView, CommandBar, InfoBar, NumberBox, CalendarView, PersonPicture, PipsPager, ProgressRing, SplitButton, Flyouts, Wizards, Notifications, LoginControl, StatusBar, PropertyPanel, and more | All WPF desktop projects |
| [`Invitrek.Foundation.Presentation.Docking.Contracts`](#27-invitrekfoundationpresentationdockingcontracts) | Docking framework contracts — `IDockManager`, `IDockToolWindow`, `IDockLayoutStore`, `ToolWindowAttribute` | All dockable shell projects |
| [`Invitrek.Foundation.Presentation.Docking`](#28-invitrekfoundationpresentationdocking) | Docking framework implementation — multi-pane shell, layout persistence, tool window registry | WPF shell projects |
| [`Invitrek.Foundation.Presentation.CodeEditor`](#29-invitrekfoundationpresentationcodeeditor) | Syntax-highlighted code editor control (WPF) with C#, C++, CSS, HTML, JS, SQL language support, IntelliSense hook, theming | DeveloperStudio, Html5Designer |
| [`Invitrek.Foundation.Presentation.DesignerCanvas`](#30-invitrekfoundationpresentationdesignercanvas) | Interactive design-surface canvas — element manipulation, alignment, auto-layout, real-time collaboration events | Html5Designer, visual design tools |
| [`Invitrek.Foundation.TaskDialogs`](#31-invitrekfoundationtaskdialogs) | Fluent Windows task dialog builder (WPF) | All WPF desktop projects |
| [`Invitrek.Foundation.Localization`](#32-invitrekfoundationlocalization) | UI localization helpers, `MessageBoxHelper`, `MessageBoxDialogExtensions` | All localized projects |
| [`Invitrek.Foundation.Images`](#33-invitrekfoundationimages) | Centralized image/icon resource library | All WPF desktop projects |
| [`Invitrek.Foundation.WindowsAPICodePack`](#34-invitrekfoundationwindowsapicodepack) | Windows Shell integration — file dialogs, taskbar, jump lists, sensor APIs | Desktop shell projects |
| [`Invitrek.Foundation.Windows.Forms`](#35-invitrekfoundationwindowsforms) | WinForms base controls and helpers | WinForms projects |
| [`Invitrek.Foundation.Drawing`](#36-invitrekfoundationdrawing) | GDI+ drawing helpers and extensions | WinForms / drawing projects |

---

## 1. `Invitrek.Foundation.Core`

**Assembly:** `Invitrek.Foundation.Core`  
**Project:** `src/Foundation/BCL/Core/Foundation.Core.csproj`  
**Targets:** `net48` + `net10.0`  
**Required by:** Every domain project.

The kernel of the platform. Provides all cross-cutting concerns used throughout every layer of every domain application. Contains no external NuGet dependencies — only BCL.

> **Full API samples:** [`docs/Foundation/Core/Core/API-SAMPLES.md`](../../docs/Foundation/Core/Core/API-SAMPLES.md)

### 1.1 Logging

Static, application-wide logger. All tools and services route through this instead of calling `Console.WriteLine` directly.

```csharp
// Namespace: Invitrek.Foundation.Logging

// Configure once at startup (assign a real provider):
Logger.Instance = new ConsoleLogger("MyApp", LogLevel.Information);
// Or via NLog/Serilog packages (see §16, §17)

// Use anywhere (static, no injection required):
Logger.LogInformation("Processing {0} records", count);
Logger.LogWarning("Retry attempt {0}", attempt);
Logger.LogError(exception, "Failed to save customer {0}", customerId);
Logger.LogFatal("Unrecoverable error — shutting down");
```

Key types: `Logger` (static), `ILogger`, `ILoggerFactory`, `ILoggerProvider`, `LogLevel`, `ConsoleLogger`, `FileLogger`, `RollingFileLogger`, `AggregatorLogger`.

---

### 1.2 CLI Framework (`ConsoleAppBuilder`)

Convention-based CLI host — discovers all `[Command]`-decorated classes in an assembly, binds arguments to `[Option]`-decorated properties, routes to `ICommand.ExecuteAsync`, and provides built-in `help`.

```csharp
// Namespace: Invitrek.Foundation.CommandLine

// Startup (Program.cs):
await ConsoleAppBuilder
    .Create("devtools")
    .UseLoggerCategory("devtools")
    .AddCommandsFromAssembly(Assembly.GetExecutingAssembly())
    .RunAsync(args);

// Define a command:
[Command("sync-standards", Description = "Sync org engineering standards into this repo")]
public class SyncStandardsCommand : ICommand
{
    [Option("--root", Description = "Repository root path")]
    public string Root { get; set; }

    [Option("--force", Description = "Overwrite unchanged files")]
    public bool Force { get; set; }

    public async Task<int> ExecuteAsync(CommandContext context)
    {
        // implementation
        return 0;
    }
}
```

Key types: `ConsoleAppBuilder`, `ICommand`, `CommandAttribute`, `OptionAttribute`, `CommandContext`.

---

### 1.3 Dependency Injection

Lightweight, framework-agnostic DI container. Integrates with `Microsoft.Extensions.DependencyInjection` on .NET 10 and provides its own simple container for .NET Framework 4.8.

```csharp
// Namespace: Invitrek.Foundation.DependencyInjection

// Mark implementation for auto-discovery:
[DependencyService(typeof(ICustomerService), ServiceLifetime.Scoped)]
internal class CustomerService : ICustomerService { ... }

// Register manually:
IServiceRegistry registry = new SimpleServiceContainer();
registry.Register<IOrderService, OrderService>(ServiceLifetime.Transient);
registry.RegisterSingleton<ICache>(new MemoryCacheProvider());
IServiceProvider provider = registry.Build();

// Resolve:
var svc = provider.GetService<IOrderService>();
```

Key types: `DependencyServiceAttribute`, `IServiceRegistry`, `IServiceScope`, `SimpleServiceContainer`, `ServiceLifetime`.

---

### 1.4 Object Mapping (`ObjectMapper`)

IL-generated, convention-based object-to-object mapper. Maps by property name (case-insensitive), handles nullable conversions, numeric widening, and inheritance. Cached after first compilation.

```csharp
// Namespace: Invitrek.Foundation.Mapping

// DTO → Entity:
var entity = ObjectMapper.Map<CustomerDto, Customer>(dto);

// Entity → ViewModel (into existing instance):
var vm = new CustomerViewModel();
ObjectMapper.Map<Customer, CustomerViewModel>(entity, vm);

// Collection mapping:
var viewModels = ObjectMapper.MapList<Customer, CustomerViewModel>(customers);

// Custom override:
var config = new MapperConfiguration();
config.For<CustomerDto, Customer>()
      .Override(src => src.FullName, dest => dest.Name);
var mapper = new ReflectionMapper(config);
```

Key types: `IMapper`, `ObjectMapper` (static, IL-backed), `ReflectionMapper`, `MapperConfiguration`, `FluentMapperConfigurationBuilder`, `MapToAttribute`.

---

### 1.5 Change Tracking (`DirtyCapable` / `PropertyChangeTracker`)

Built-in property change tracking for entities and view models. Records original values, detects mutations, supports undo, and serializes the change set for over-the-wire patch operations.

```csharp
// Namespace: Invitrek.Foundation.Data.ChangeTracking

public class Customer : DirtyCapable
{
    private string _name;
    public string Name
    {
        get => _name;
        set => SetProperty(ref _name, value); // auto-tracks
    }
}

var c = new Customer { Name = "Alice" };
c.ChangeTracker.BeginTracking();
c.Name = "Bob";

bool isDirty   = c.IsDirty;                          // true
var changes    = c.ChangeTracker.GetChanges();        // { Name: "Alice" → "Bob" }
c.ChangeTracker.Undo();                               // reverts to "Alice"

// Serialize tracker to send as patch request:
var patch = c.ChangeTracker; // DataContract-serializable
```

Key types: `DirtyCapable`, `PropertyChangeTracker`, `PropertyChangedInfo`, `IDirtyCapable`, `IChangeTrackable`, `ObjectStateChangeTracker`.

---

### 1.6 Validation

Attribute-driven and fluent validation pipeline with IL-generated validators for high-throughput scenarios.

```csharp
// Namespace: Invitrek.Foundation.Validation

// Fluent configuration:
var builder = new ValidationConfigurationBuilder<CustomerRequest>();
builder.For(x => x.Name).Required().MaxLength(100);
builder.For(x => x.Email).Required().Email();

IValidator<CustomerRequest> validator = builder.Build();

// Use:
ValidationResult result = validator.Validate(request);
if (!result.IsValid)
    foreach (var error in result.Errors)
        Console.WriteLine($"{error.PropertyName}: {error.Message}");
```

Key types: `IValidator<T>`, `ValidationResult`, `ValidationError`, `ValidationConfigurationBuilder`, `Validator` (static), `OrderedValidator`, `ValidationPriority`.

---

### 1.7 CQRS Dispatcher

Lightweight CQRS command and query bus. Decouples feature handlers from callers. Supports both in-memory and extended transports.

```csharp
// Namespace: Invitrek.Foundation.CQRS

// Define a command:
public class CreateOrderCommand : ICommand<int>
{
    public string CustomerCode { get; set; }
    public List<OrderLine> Lines { get; set; }
}

// Define a handler:
public class CreateOrderHandler : ICommandHandler<CreateOrderCommand, int>
{
    public async Task<int> HandleAsync(CreateOrderCommand cmd, CancellationToken ct)
    {
        // ... persist order, return new ID
    }
}

// Dispatch from service:
int orderId = await _commandDispatcher.DispatchAsync<CreateOrderCommand, int>(cmd, ct);

// Query:
var result = await _queryDispatcher.DispatchAsync<GetOrderQuery, OrderDto>(query, ct);
```

Key types: `ICommandDispatcher`, `IQueryDispatcher`, `ICommandHandler<TCmd,TResult>`, `IQueryHandler<TQuery,TResult>`, `ICommand`, `IQuery<TResult>`, `InMemoryCommandDispatcher`, `InMemoryQueryDispatcher`.

---

### 1.8 Messaging Bus

Publish/subscribe message bus. `InMemoryMessageBus` ships in-box; RabbitMQ provider available separately (see §12).

```csharp
// Namespace: Invitrek.Foundation.Messaging

public class OrderPlacedMessage : MessageBase
{
    public int OrderId { get; set; }
}

// Publisher:
await _publisher.PublishAsync(new OrderPlacedMessage { OrderId = 42 }, ct);

// Subscriber:
await _subscriber.SubscribeAsync<OrderPlacedMessage>(async (msg, ct) =>
{
    await SendConfirmationEmail(msg.OrderId);
}, ct);
```

Key types: `IMessagePublisher`, `IMessageSubscriber`, `IMessage`, `MessageBase`, `InMemoryMessageBus`.

---

### 1.9 Caching Abstraction

Provider-agnostic cache interface. Memory and Redis providers are separate packages (see §14, §15).

```csharp
// Namespace: Invitrek.Foundation.Caching

// Set with TTL:
_cache.Set("customer:42", customer, TimeSpan.FromMinutes(10));

// Set with absolute expiry:
_cache.Set("session:abc", session, DateTime.UtcNow.AddHours(1));

// Get:
var customer = _cache.Get<Customer>("customer:42");

// Remove:
_cache.Remove("customer:42");

// Exists:
bool exists = _cache.Exists("customer:42");
```

Key types: `ICache`, `ICacheProvider`, `ICacheConfiguration`, `CacheType`, `SerializationFormat`.

---

### 1.10 Serialization

Unified serializer abstraction with built-in JSON (System.Text.Json) and XML implementations. All caching and service layer code routes through `ISerializer`.

```csharp
// Namespace: Invitrek.Foundation.Serialization

// JSON (default — System.Text.Json):
ISerializer json = new JsonSerializerDotNet();
string s   = (string)json.Serialize(customer);
Customer c = (Customer)json.Deserialize(typeof(Customer), s);

// Use static Serializer helper:
string payload = Serializer.Current.SerializeToString(order);
Order  order   = Serializer.Current.Deserialize<Order>(payload);
```

Key types: `ISerializer`, `IJsonSerializer`, `IJsonSerializerFactory`, `JsonSerializerDotNet`, `XmlSerializer`, `Serializer` (static helper), `SerializationFormat`.

---

### 1.11 Security

Authentication, password hashing (PBKDF2, SHA256, SHA512), AES encryption, membership contracts, permission model.

```csharp
// Namespace: Invitrek.Foundation.Security

// Hash a password (PBKDF2):
IPasswordHasher hasher = new Pbkdf2PasswordHasher();
string hash   = hasher.HashPassword(plainText);
bool   isOk   = hasher.VerifyPassword(plainText, hash) == PasswordVerificationResult.Success;

// AES encryption:
IEncryptionService enc = EncryptionServiceFactory.Create(EncryptionTypes.Aes);
string cipherText = enc.Encrypt("sensitive-data");
string plain      = enc.Decrypt(cipherText);

// Password strength:
var meter = new PasswordStrengthMeter();
int score = meter.Score("P@ssw0rd!");  // 0–100
```

Key types: `IPasswordHasher`, `IEncryptionService`, `IHashingService`, `IAuthorizationManager`, `IMembershipService`, `UserPrincipal`, `PasswordStrengthMeter`, `Permission`, `Policy`.

---

### 1.12 Tenant Model

Ambient tenant context — provides the current user's tenant identity across all layers without threading through constructor parameters.

```csharp
// Namespace: Invitrek.Foundation.TenantModel

// Set at login (e.g., in auth middleware):
TenantContext.SwitchContext(new TenantUserPrincipal
{
    ClientCode  = tenantGuid,
    TenantID    = 7,
    CompanyID   = "ACME",
    Name        = "john.doe"
});

// Access anywhere in the call chain:
var tenant = TenantContext.Current;
int  id     = tenant.TenantID;
Guid client = tenant.ClientCode;
```

Entities that belong to a tenant implement `ITenantOwnedEntity<TKey>` which exposes `TenantID`.

Key types: `TenantContext` (static), `ITenantUserPrincipal`, `ITenantOwnedEntity<TKey>`, `TenantAccountInfo`.

---

### 1.13 Auditing Contracts

Standard audit trail contracts. Entities implement `IAuditableEntity<TKey>` (full) or `IAuditableEntityReadOnly<TKey>` (creation-only).

```csharp
// Namespace: Invitrek.Foundation.Auditing

public class Order : DirtyCapable, IAuditableEntity<int>
{
    public AuditUser<int> CreatedUser  { get; private set; }
    public DateTime       CreatedDate  { get; private set; }
    public AuditUser<int> ModifiedUser { get; private set; }
    public DateTime       ModifiedDate { get; private set; }
}
```

Key types: `IAuditableEntity<TKey>`, `IAuditableEntityReadOnly<TKey>`, `AuditUser<TKey>`.

---

### 1.14 Middleware Pipeline (`IMiddleware`)

AOP-style middleware pipeline for decorating service method invocations (logging, validation, auth, retry) without modifying service code.

```csharp
// Namespace: Invitrek.Foundation.Middleware

public class ValidationMiddleware : IMiddleware
{
    public object Invoke(InvocationContext ctx, Func<object> next)
    {
        // Pre-processing: validate first argument
        if (ctx.Arguments.Length > 0)
            _validator.Validate(ctx.Arguments[0]);
        return next();  // call the real method
    }
}
```

Key types: `IMiddleware`, `MiddlewareAttribute`, `IInterceptor`, `InterceptorPipeline`, `InvocationContext`.

---

### 1.15 Runtime — Property Access Strategies

IL-generated vs. reflection property access. Used internally by `ObjectMapper`, argument binding in `ConsoleAppBuilder`, and other reflection-heavy paths. Strategy is configurable at startup or via environment variable.

```csharp
// Namespace: Invitrek.Foundation.Runtime.Interception

// Force IL strategy globally (fastest):
PropertyAccessStrategyProvider.Use(PropertyAccessStrategyKind.Il);

// Force reflection (easiest to debug):
PropertyAccessStrategyProvider.Use(PropertyAccessStrategyKind.Reflection);

// Or via environment variable (no code change needed):
// INVITREK_BINDING_STRATEGY=il
```

Key types: `PropertyAccessStrategyProvider`, `IPropertyAccessStrategy`, `PropertyAccessStrategyKind`, `IlPropertyAccessStrategy`, `ReflectionPropertyAccessStrategy`.

---

### 1.16 Web Utilities

Query string building, MIME type helpers, web response models.

```csharp
// Namespace: Invitrek.Foundation.Web

var qs = new QueryStringBuilder()
    .Add("page", 1)
    .Add("size", 25)
    .Add("filter", "active")
    .Build();
// → "?page=1&size=25&filter=active"

string mime = MimeHelper.GetMimeType(".pdf"); // → "application/pdf"
```

Key types: `QueryStringBuilder`, `MimeHelper`, `MimeTypes`, `WebServiceResponse`, `WebServiceResponseStatus`.

---

## 2. `Invitrek.Foundation.Data`

**Assembly:** `Invitrek.Foundation.Data`  
**Project:** `src/Foundation/DataAccess/Data/Foundation.Data.csproj`  
**Targets:** `net48` + `net10.0`  
**Required by:** All domain projects with database access.

ORM-free, ADO.NET-backed repository framework. No Entity Framework dependency. Provides a CQRS-style repository pattern with compiled IL queries, change-tracking-aware partial updates, and multi-database CQRS (read replica + write master) out of the box.

> **Full API samples:** [`docs/Foundation/Data/Data/API-SAMPLES.md`](../../docs/Foundation/Data/Data/API-SAMPLES.md)

### Key contracts

```csharp
// Namespace: Invitrek.Foundation.Data

// Entity contract — all table-mapped POCOs implement this:
public class Customer : DirtyCapable, IDbEntity<int>
{
    [Key]
    public int Id { get; set; }

    [Column("CustomerName")]
    public string Name { get; set; }

    public string Email { get; set; }
}

// Repository contract:
public interface ICustomerRepository : IDbRepository<Customer, int>
{
    Task<IEnumerable<Customer>> GetActiveAsync(CancellationToken ct);
}

// Derived concrete repository (no configuration needed):
public class CustomerRepository : BaseRepository<Customer, int>, ICustomerRepository
{
    public CustomerRepository(IDbContextFactory factory) : base(factory) { }

    public async Task<IEnumerable<Customer>> GetActiveAsync(CancellationToken ct)
        => await Query.GetAllAsync(x => x.IsActive, ct);
}
```

### Standard CRUD via `IDbRepository<T, TKey>`

```csharp
// Reads (Query side):
Customer   c  = await repo.GetByIdAsync(42, ct);
IEnumerable<Customer> all     = await repo.GetAllAsync(ct);
IEnumerable<Customer> active  = await repo.GetAllAsync(x => x.IsActive, ct);
PagedResult<Customer> page    = await repo.GetPagedAsync(filter, page: 1, size: 25, ct);

// Writes (Command side):
await repo.AddAsync(newCustomer, ct);
await repo.UpdateAsync(customer, ct);     // change-tracking-aware — only dirty columns
await repo.DeleteAsync(42, ct);
await repo.DeleteAsync(x => x.IsActive == false, ct);

// Bulk:
await repo.BulkInsertAsync(customers, ct);
await repo.BulkUpdateAsync(customers, ct);

// Raw SQL:
var results = await repo.QueryAsync<CustomerSummary>(
    "SELECT CustomerID AS Id, Name FROM Customer WHERE TenantID = @TenantID",
    new { TenantID = 7 }, ct);

// Aggregates:
int    count = await repo.CountAsync(x => x.IsActive, ct);
decimal sum  = await repo.SumAsync(x => x.Balance, ct);
```

### Unit of Work (multi-entity transactions)

```csharp
await using var uow = new FluentUnitOfWork(dbContext);
await uow.ExecuteAsync(async () =>
{
    await uow.Repository<Order, int>().AddAsync(order, ct);
    await uow.Repository<Invoice, int>().AddAsync(invoice, ct);
});  // commits on success, rolls back on exception
```

Key types: `IDbEntity<TKey>`, `IDbRepository<TEntity,TKey>`, `IQueryRepository<TEntity,TKey>`, `ICommandRepository<TEntity,TKey>`, `BaseRepository<TEntity,TKey>`, `EntitySet<TEntity,TKey>`, `IDbContext`, `IDbContextFactory`, `FluentUnitOfWork`, `FluentQuery`, `FluentCommand`, `DatabaseRepository`, `AuditableEntity`.

---

## 3. `Invitrek.Foundation.Data.SqlServer`

**Assembly:** `Invitrek.Foundation.Data.SqlServer`  
**Project:** `src/Foundation/DataAccess/Providers/SqlServer/Foundation.Data.SqlServer.csproj`  
**Targets:** `net48` + `net10.0`

SQL Server ADO.NET provider. Implements `IDbContextFactory` and `IDbContext` for SQL Server. Registers via DI.

```csharp
services.AddFoundationData(options =>
{
    options.UseProvider(DatabaseProvider.SqlServer);
    options.QueryConnectionString  = Configuration["SqlServer:ReadReplica"];
    options.CommandConnectionString = Configuration["SqlServer:Master"];
});
```

---

## 4. `Invitrek.Foundation.Data.PostgreSql`

**Assembly:** `Invitrek.Foundation.Data.PostgreSql`  
**Project:** `src/Foundation/DataAccess/Providers/PostgreSql/Foundation.Data.PostgreSql.csproj`  
**Targets:** `net48` + `net10.0`

PostgreSQL provider. Same API as §3 — swap `DatabaseProvider.SqlServer` → `DatabaseProvider.PostgreSql`.

---

## 5. `Invitrek.Foundation.Data.MySql`

**Assembly:** `Invitrek.Foundation.Data.MySql`  
**Project:** `src/Foundation/DataAccess/Providers/MySql/Foundation.Data.MySql.csproj`  
**Targets:** `net48` + `net10.0`

MySQL provider. Conditionally registered for MySQL and MariaDB.

---

## 6. `Invitrek.Foundation.Data.Sqlite`

**Assembly:** `Invitrek.Foundation.Data.Sqlite`  
**Project:** `src/Foundation/DataAccess/Providers/Sqlite/Foundation.Data.Sqlite.csproj`  
**Targets:** `net48` + `net10.0`

SQLite provider for embedded/local database scenarios and test isolation.

```csharp
// Ideal for unit tests with an isolated in-memory DB:
services.AddFoundationData(o =>
{
    o.UseProvider(DatabaseProvider.Sqlite);
    o.CommandConnectionString = "Data Source=:memory:";
});
```

---

## 7. `Invitrek.Foundation.Data.Oracle`

**Assembly:** `Invitrek.Foundation.Data.Oracle`  
**Project:** `src/Foundation/DataAccess/Providers/Oracle/Foundation.Data.Oracle.csproj`  
**Targets:** `net48` + `net10.0`

Oracle Database provider.

---

## 8. `Invitrek.Foundation.ServiceModel`

**Assembly:** `Invitrek.Foundation.ServiceModel`  
**Project:** `src/Foundation/Communication/ServiceModel/Foundation.ServiceModel.csproj`  
**Targets:** `net48` + `net10.0`  
**Required by:** All projects exposing or consuming services over WCF, gRPC, REST, or SignalR.

Provides `ServiceBase` — the mandatory base class for all service implementations. Wraps every operation in a structured execution pipeline with automatic logging, performance metrics (`METRIC` log entries), and error handling hooks. A **single implementation** targets all protocols; protocol wiring is applied at the host layer.

> **Full API samples (§8–§12):** [`docs/Foundation/Communication/ServiceModel/API-SAMPLES.md`](../../docs/Foundation/Communication/ServiceModel/API-SAMPLES.md)

```csharp
// Namespace: Invitrek.Foundation.ServiceModel

[ServiceContract]
public interface ICustomerService
{
    [OperationContract]
    Task<EntityListResult<Customer>> GetCustomerListAsync(CustomerListRequest request, CancellationToken token);

    [OperationContract]
    Task<EntitySavedResult<Customer>> SaveCustomerAsync(EntitySaveRequest<Customer> request, CancellationToken token);
}

// Single implementation — endpoint-agnostic:
[DependencyService(typeof(ICustomerService))]
public class CustomerService : ServiceBase, ICustomerService
{
    private readonly ICustomerRepository _repo;
    public CustomerService(ICustomerRepository repo) => _repo = repo;

    public Task<EntityListResult<Customer>> GetCustomerListAsync(
        CustomerListRequest request, CancellationToken token)
        => ExecuteAsync(request, async req =>
        {
            var data = await _repo.GetPagedAsync(req.Filter, req.Page, req.PageSize, token);
            return new EntityListResult<Customer>(data);
        });
}
```

### Service request / response base types

| Type | Use |
|------|-----|
| `EntitySaveRequest<T>` | Create or update an entity (carries `IsNew` flag) |
| `EntityCreateRequest<T>` | Explicit create request |
| `EntityUpdateRequest<T>` | Explicit update request |
| `EntityGetResponse<T>` | Single-entity response |
| `EntityListRequest` | Paginated list request (page, size, filter) |
| `EntityActionResponse` | Generic outcome (success / error / message) |

Key types: `ServiceBase`, `EntitySaveRequest<T>`, `EntityCreateRequest<T>`, `EntityUpdateRequest<T>`, `EntityGetResponse<T>`, `EntityActionResponse`, `IEntity<T>`, endpoint attributes (`EndpointAddressSuffixAttribute`).

---

## 9. `Invitrek.Foundation.ServiceModel.Hosting`

**Assembly:** `Invitrek.Foundation.ServiceModel.Hosting`  
**Project:** `src/Foundation/Communication/ServiceModelHosting/Foundation.ServiceModel.Hosting.csproj`  
**Targets:** `net48` only

.NET Framework `ServiceHost`-based WCF hosting. Used by `ServiceModel.WindowsService` on `net48` targets.

```csharp
// net48 WCF host bootstrap:
var host = new FoundationServiceHost(typeof(CustomerService));
host.Open();
```

---

## 10. `Invitrek.Foundation.ServiceModel.AspNetCore`

**Assembly:** `Invitrek.Foundation.ServiceModel.AspNetCore`  
**Project:** `src/Foundation/Communication/ServiceModel.Providers/Foundation.ServiceModel.AspNetCore.csproj`  
**Targets:** `net10.0` only

ASP.NET Core / CoreWCF hosting provider for .NET 10. Configures the CoreWCF middleware pipeline and maps service endpoints.

```csharp
// net10 host (Program.cs):
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddFoundationServiceModel();
builder.Services.AddScoped<ICustomerService, CustomerService>();

var app = builder.Build();
app.UseFoundationServiceModel(endpoints =>
{
    endpoints.AddService<ICustomerService>();
});
app.Run();
```

---

## 11. `Invitrek.Foundation.ServiceModel.Proxy`

**Assembly:** `Invitrek.Foundation.ServiceModel.Proxy`  
**Project:** `src/Foundation/Communication/ServiceModel.Proxy/Foundation.ServiceModel.Proxy.csproj`  
**Targets:** `net48` + `net10.0`

Client-side proxy base classes and channel factory abstractions. Consumers code against `ICustomerService` only; the proxy wires the transport.

```csharp
// Namespace: Invitrek.Foundation.ServiceModel

// WCF channel proxy (net48 + net10):
var proxy = new ServiceClientProxy<ICustomerService>(
    endpointAddress: "net.tcp://server:8000/CustomerService");

var result = await proxy.Channel.GetCustomerListAsync(request, ct);

// Or register via DI (recommended):
services.AddServiceProxy<ICustomerService>(options =>
{
    options.EndpointAddress = Configuration["Services:CustomerService:Endpoint"];
    options.Protocol        = Protocol.NetTcp;
});
```

Key types: `ClientChannel<T>`, `ClientChannelFactoryBase`, `IClientChannelProvider`, `IConnectionManager`, `ChannelConfiguration`.

---

## 12. `Invitrek.Foundation.Messaging.RabbitMQ`

**Assembly:** `Invitrek.Foundation.Messaging.RabbitMQ`  
**Project:** `src/Foundation/Communication/MessagingRabbitMQ/Foundation.Messaging.RabbitMQ.csproj`  
**Targets:** `net48` + `net10.0`

RabbitMQ provider for `IMessagePublisher` and `IMessageSubscriber` (see §1.8). Drop-in replacement for `InMemoryMessageBus` in distributed deployments.

```csharp
services.AddRabbitMqMessaging(options =>
{
    options.HostName   = "rabbitmq-server";
    options.Exchange   = "invitrek.events";
    options.RetryCount = 3;
});
// IMessagePublisher / IMessageSubscriber resolve to RabbitMQ-backed implementations
```

---

## 13. `Invitrek.Foundation.CodeModel`

**Assembly:** `Invitrek.Foundation.CodeModel`  
**Project:** `src/Foundation/CodeModel/Core/Foundation.CodeModel.csproj`  
**Targets:** `net48` + `net10.0`  
**Required by:** DevTools, code generators, scaffolding automation.

Fluent, strongly typed C# code-generation API. **Never use raw `StringBuilder` string concatenation for C# generation** — always use `CSharpCodeBuilder` instead. Prevents generation drift, enforces compile-time safety, and produces consistent, formatted output.

> **Full API samples:** [`docs/Foundation/CodeModel/Core/API-SAMPLES.md`](../../docs/Foundation/CodeModel/Core/API-SAMPLES.md)

```csharp
// Namespace: Invitrek.Foundation.CodeModel

// Build a namespace + class + properties:
var ns = CSharpCodeBuilder.CreateNamespace("Invitrek.Nexus.Repository.Entities");

var cls = ns.AddClass("Customer")
            .IsPublic()
            .IsPartial()
            .InheritsFrom("EntityBase<int>")
            .ImplementsInterface("IDbEntity<int>");

cls.AddProperty(nameof(Customer.Name), typeof(string))
   .IsPublic()
   .HasGetter()
   .HasSetter()
   .AddAttribute("[Column(\"CustomerName\")]");

cls.AddProperty("Email", typeof(string))
   .IsPublic()
   .HasGetter()
   .HasSetter();

// Write to string:
string code = new CSharpCodeWriter(ns.Definition).Write();

// Write to file:
new CSharpCodeFileWriter(ns.Definition)
    .WriteToFile("Generated/Customer.cs");
```

Key types: `CSharpCodeBuilder`, `NamespaceBuilder`, `ClassBuilder`, `ClassPropertyBuilder`, `ClassMethodBuilder`, `ClassConstructorBuilder`, `CSharpCodeWriter`, `CSharpCodeFileWriter`, `CSharpProgramBuilder`.

---

## 14. `Invitrek.Foundation.Caching.Memory`

**Assembly:** `Invitrek.Foundation.Caching.Memory`  
**Project:** `src/Foundation/BCL/Caching.Providers/Memory/Foundation.Caching.Memory.csproj`  
**Targets:** `net48` + `net10.0`

In-process memory cache. Implements `ICache` (see §1.9). Suitable for single-instance deployments.

> **Full API samples:** [`docs/Foundation/Caching/Memory/API-SAMPLES.md`](../../docs/Foundation/Caching/Memory/API-SAMPLES.md)

```csharp
services.AddFoundationCaching(o => o.UseMemory());
// Resolves ICache → MemoryCacheProvider
```

---

## 15. `Invitrek.Foundation.Caching.Redis`

**Assembly:** `Invitrek.Foundation.Caching.Redis`  
**Project:** `src/Foundation/BCL/Caching.Providers/Redis/Foundation.Caching.Redis.csproj`  
**Targets:** `net48` + `net10.0`

Redis distributed cache provider. Implements `ICache`. Use in multi-instance / load-balanced deployments.

> **Full API samples:** [`docs/Foundation/Caching/Redis/API-SAMPLES.md`](../../docs/Foundation/Caching/Redis/API-SAMPLES.md)

```csharp
services.AddFoundationCaching(o =>
{
    o.UseRedis();
    o.RedisConnectionString = "redis-server:6379,password=secret";
});
```

---

## 16. `Invitrek.Foundation.Logging.NLog`

**Assembly:** `Invitrek.Foundation.Logging.NLog`  
**Project:** `src/Foundation/BCL/Logging.Providers/NLog/Foundation.Logging.NLog.csproj`  
**Targets:** `net48` + `net10.0`

Connects Foundation `Logger` to NLog. Configure once at startup — all subsequent `Logger.LogXxx(...)` calls route to NLog.

> **Full API samples:** [`docs/Foundation/Logging/NLog/API-SAMPLES.md`](../../docs/Foundation/Logging/NLog/API-SAMPLES.md)

```csharp
Logger.Instance = new NLogLoggerFactory().CreateLogger("MyApp");
```

---

## 17. `Invitrek.Foundation.Logging.Serilog`

**Assembly:** `Invitrek.Foundation.Logging.Serilog`  
**Project:** `src/Foundation/BCL/Logging.Providers/Serilog/Foundation.Logging.Serilog.csproj`  
**Targets:** `net48` + `net10.0`

Connects Foundation `Logger` to Serilog. Structured logging to any Serilog sink.

> **Full API samples:** [`docs/Foundation/Logging/Serilog/API-SAMPLES.md`](../../docs/Foundation/Logging/Serilog/API-SAMPLES.md)

```csharp
var serilog = new LoggerConfiguration()
    .WriteTo.Console()
    .WriteTo.File("logs/app.log", rollingInterval: RollingInterval.Day)
    .CreateLogger();

Logger.Instance = new SerilogLoggerFactory(serilog).CreateLogger("MyApp");
```

---

## 18. `Invitrek.Foundation.Telemetry`

**Assembly:** `Invitrek.Foundation.Telemetry`  
**Project:** `src/Foundation/BCL/Telemetry/Foundation.Telemetry.csproj`  
**Targets:** `net48` + `net10.0`

Application telemetry abstractions — counters, gauges, histograms, distributed tracing. `ServiceBase` (§8) automatically emits operation duration metrics using this layer.

```csharp
// Namespace: Invitrek.Foundation.Telemetry

[Metric("customer.save.duration")]
public async Task SaveCustomerAsync(Customer c) { ... }
```

---

## 19. `Invitrek.Foundation.UIAutomation`

**Assembly:** `Invitrek.Foundation.UIAutomation`  
**Project:** `src/Foundation/UIAutomation/Foundation.UIAutomation.csproj`  
**Targets:** `net48` + `net10.0`

UI test automation abstractions with provider-specific packages for desktop (WinForms/WPF), web (Selenium/Playwright), and mobile (Appium). Domain test projects reference the abstraction; CI pipelines swap providers.

```csharp
// Provider packages (pick one):
// Foundation.UIAutomation.Desktop  — WinForms/WPF
// Foundation.UIAutomation.Web      — Web browsers
// Foundation.UIAutomation.Mobile   — Mobile (Appium)
```

---

## 20. `Invitrek.Foundation.Workflow`

**Assembly:** `Invitrek.Foundation.Workflow`  
**Project:** `src/Foundation/Workflow/Foundation.Workflow.csproj`  
**Targets:** `net48` + `net10.0`

Workflow engine abstractions. Provider packages support Windows Workflow Foundation (`net48`), Elsa Workflows, and WorkflowCore. Domain projects reference the abstraction — provider is swapped at the host layer.

```
// Provider packages (pick one per target):
// Foundation.Workflow.WindowsWorkflow  (net48)
// Foundation.Workflow.Elsa             (net10)
// Foundation.Workflow.WorkflowCore     (net48 + net10)
```

---

## 21. `Invitrek.Foundation.AI`

**Assembly:** `Invitrek.Foundation.AI`  
**Project:** `src/Foundation/AI/Foundation.AI.csproj`  
**Targets:** `net48` + `net10.0`  
**Status:** Planned / under development (pre-MVP)

Provider-agnostic AI integration abstractions. When complete, domain projects will reference this package and configure a provider at the host layer.

```
// Planned provider packages:
// Foundation.AI.AzureAI     — Azure AI / Azure OpenAI
// Foundation.AI.OpenAI      — OpenAI directly
// Foundation.AI.Ollama      — Local Ollama
// Foundation.AI.LlamaCpp    — Local LlamaCpp
```

---

## 22. `Invitrek.Foundation.Security`

**Assembly:** `Invitrek.Foundation.Security`  
**Project:** `src/Foundation/Identity/Security/Security.csproj`  
**Targets:** `net48` + `net10.0`

Full identity and security stack: membership, authentication flow, authorization policies, PBKDF2/SHA256/SHA512 hashing, AES/HMAC encryption, JWT support (via `Security.JWT` provider), OAuth (via `OAuth` provider), and certificate-based auth (via `Certificate` provider).

```csharp
// Membership creation:
IMembershipService membership = ...;
MembershipCreateStatus status = await membership.CreateUserAsync(
    username: "jane.doe",
    password: "P@ssw0rd!",
    email:    "jane@acme.com");

// Login:
AuthenticationResult result = await membership.AuthenticateAsync(
    new AuthenticationRequest { Username = "jane.doe", Password = "P@ssw0rd!" });

if (result.Status == LoginStatus.Success)
    TenantContext.SwitchContext(result.Principal);
```

Provider packages:

| Package | Transport |
|---------|-----------|
| `Invitrek.Foundation.Security.JWT` | JWT bearer tokens |
| `Invitrek.Foundation.Security.OAuth` | OAuth2 / OIDC |
| `Invitrek.Foundation.Security.Certificate` | X.509 certificate auth |

---

## Cross-Cutting Dependency Map

```
All domain projects
        │
        ├── Invitrek.Foundation.Core          ← mandatory (logging, DI, mapping, validation, ...)
        │
        ├── Invitrek.Foundation.Data          ← if DB access
        │   └── + one provider (SqlServer / PostgreSql / MySql / Sqlite / Oracle)
        │
        ├── Invitrek.Foundation.ServiceModel  ← if exposing or consuming services
        │   ├── + Hosting  (net48)  or  AspNetCore (net10)  ← host projects only
        │   └── + Proxy                                      ← client projects
        │
        ├── Invitrek.Foundation.Security      ← if auth/identity required
        │   └── + JWT / OAuth / Certificate provider
        │
        ├── Invitrek.Foundation.CodeModel     ← DevTools / code generators only
        │
        ├── Invitrek.Foundation.Caching.*     ← if caching required
        ├── Invitrek.Foundation.Logging.*     ← if structured logging sink needed
        ├── Invitrek.Foundation.Messaging.*   ← if message bus required
        ├── Invitrek.Foundation.Workflow      ← if workflow features required
        └── Invitrek.Foundation.AI            ← if AI features required (planned)
```

> **Note:** During pre-MVP active development all of the above are consumed as **direct project references**. Post-MVP they become **NuGet package references** (`Invitrek.Foundation.*`). See `architecture-guidelines.md §11.3`.

---

## UIFramework Libraries

> **UIFramework current state:** WPF-first. Full enterprise WPF control suite, MVVM infrastructure, docking, code editor, design-surface canvas, dialogs, theming, and Windows Shell integration.
>
> **Roadmap:** Android (MAUI), iOS (MAUI), and ASP.NET Core Blazor/Razor platform layers will be added as separate projects under `src/UIFramework/` following the same shared-project / conditional-compilation pattern. Platform-agnostic contracts (`IViewModel`, `IDockManager`, dialog service interfaces, navigation contracts) are already in place — MVVM code written today will be portable across future UI platforms without changes.
>
> **Targets:** `net48` + `net10.0-windows` (desktop today); additional targets will be introduced per platform.

---

### 23. `Invitrek.Foundation.UI`

**Assembly:** `Invitrek.Foundation.UI`  
**Project:** `src/UIFramework/UICore/UICore.csproj`  
**Targets:** `net48` + `net10.0-windows`  
**Required by:** All desktop / UI projects.

The platform-agnostic UI kernel. Provides dialog service contracts, navigation contracts, command infrastructure, data bar, editor button abstractions, and DnD interfaces. All higher-level UIFramework libraries depend on this.

> **Full API samples:** [`docs/UIFramework/UICore/API-SAMPLES.md`](../../docs/UIFramework/UICore/API-SAMPLES.md)

```csharp
// Namespace: Invitrek.Foundation  (and sub-namespaces)

// Dialog service (inject, never call MessageBox directly):
public class CustomerViewModel : ViewModelBase
{
    private readonly IDialogService _dialogs;
    public CustomerViewModel(IDialogService dialogs) => _dialogs = dialogs;

    private async Task DeleteAsync()
    {
        var result = await _dialogs.ShowConfirmationAsync(
            "Delete Customer",
            "This will permanently remove the customer. Continue?");

        if (result == DialogResult.Yes)
            await _repo.DeleteAsync(_customer.Id, CancellationToken.None);
    }
}

// Navigation:
_navigator.NavigateTo<CustomerListView>();
_navigator.GoBack();

// Command item (bound to toolbar/menu):
ICommandItem cmd = new CommandItem("Save", SaveCommand) { Icon = KnownIcons.Save };
```

Key areas: `IDialogService`, `IContentNavigator`, `ICommandItem`, `ICommandFactory`, `IDataEditor`, `IDragable`, `IDropable`, `IAppCommands`, `IEditCommands`, `IEntityListCommands`, `GridViewColumn`, dialog args (`DialogArgs`, `DateInputDialogArgs`, `DecimalRangeInputDialogArgs`).

---

### 24. `Invitrek.Foundation.Mvvm`

**Assembly:** `Invitrek.Foundation.Mvvm`  
**Project:** `src/UIFramework/MvvmCore/Invitrek.Foundation.Mvvm.csproj`  
**Targets:** `net48` + `net10.0-windows`  
**Required by:** All MVVM projects.

MVVM kernel. Provides `ViewModelBase` (the mandatory base for all view models), `RelayCommand`, `ObservableObject`, `IViewModel`, and command factory abstractions.

> **Full API samples:** [`docs/UIFramework/Mvvm/MvvmCore/API-SAMPLES.md`](../../docs/UIFramework/Mvvm/MvvmCore/API-SAMPLES.md)

```csharp
// Namespace: Invitrek.Foundation.Mvvm

public class DashboardViewModel : ViewModelBase
{
    private string _greeting;

    public string Greeting
    {
        get => _greeting;
        set => SetProperty(ref _greeting, value);   // INotifyPropertyChanged + change tracking
    }

    public ICommand RefreshCommand { get; }

    public DashboardViewModel(IDashboardService svc)
    {
        RefreshCommand = new RelayCommand(async () =>
        {
            IsBusy = true;
            StatusMessage = "Loading...";
            try
            {
                Greeting = await svc.GetGreetingAsync();
            }
            finally
            {
                IsBusy = false;
                StatusMessage = string.Empty;
            }
        });
    }

    protected override async Task InitializeAsync()
    {
        await base.InitializeAsync();
        await RefreshCommand.ExecuteAsync(null);
    }
}
```

Key types: `ViewModelBase`, `IViewModel`, `ObservableObject`, `RelayCommand`, `RelayCommandFactory`, `ICommandFactory`, `ICommandItem`, `CommandItem`, `CommandItemCollection`, `CheckedCommandItem`, `ViewModuleDescriptor`.

---

### 25. `Invitrek.Foundation.Mvvm.Extensions`

**Assembly:** `Invitrek.Foundation.Mvvm.Extensions`  
**Project:** `src/UIFramework/MvvmExtensions/Invitrek.Foundation.Mvvm.Extensions.csproj`  
**Targets:** `net48` + `net10.0-windows`  
**Required by:** All entity CRUD views.

Domain-level MVVM base classes. Provides strongly typed, ready-to-use base ViewModels for the full entity lifecycle (list, edit, create, paged list, search). Eliminates repetitive CRUD ViewModel boilerplate across domain projects.

```csharp
// Namespace: Invitrek.Foundation.Mvvm  (extension classes)

// Entity list with search + paging — inherit, no boilerplate needed:
public class CustomerListViewModel
    : PagedEntityListViewModel<Customer, int, CustomerListRequest>
{
    public CustomerListViewModel(ICustomerService svc) : base(svc) { }

    // Override only what you need:
    protected override CustomerListRequest BuildRequest(int page, int pageSize)
        => new CustomerListRequest { Page = page, PageSize = pageSize, Filter = SearchText };
}

// Entity edit (create + update):
public class CustomerEditViewModel : EntityEditViewModel<Customer, int>
{
    public CustomerEditViewModel(ICustomerService svc) : base(svc) { }

    protected override async Task AfterSaveAsync(Customer saved)
    {
        StatusMessage = $"Customer '{saved.Name}' saved successfully.";
    }
}
```

Key types: `EntityListViewModel<TEntity, TKey>`, `PagedEntityListViewModel<TEntity, TKey, TRequest>`, `EntityEditViewModel<TEntity, TKey>`, `EntityDetailViewModel<TEntity, TKey>`, `EditViewModelBase`, `DataSourceViewModelBase`, `AccountViewModel`, `FeedbackViewModel`, `LookupListViewModelBase`.

---

### 26. `Invitrek.Foundation.Presentation.Controls`

**Assembly:** `Invitrek.Foundation.Presentation.Controls`  
**Project:** `src/UIFramework/Controls/Presentation.Controls.csproj`  
**Targets:** `net48` + `net10.0-windows`  
**Required by:** All WPF desktop projects.

WinUI3-equivalent enterprise WPF control suite. All controls follow WinUI3 API surface, naming conventions, and theming model so the migration path to native WinUI3 (or MAUI) is minimal. Controls are fully themeable, support light/dark mode, and can be styled via XAML resource dictionaries.

```xml
<!-- NavigationView — app shell navigation -->
<ui:NavigationView x:Name="NavView"
                   PaneDisplayMode="Left"
                   SelectionChanged="NavView_SelectionChanged">
    <ui:NavigationView.MenuItems>
        <ui:NavigationViewItem Content="Dashboard"   Icon="Home"     Tag="dashboard" />
        <ui:NavigationViewItem Content="Customers"   Icon="People"   Tag="customers" />
        <ui:NavigationViewItem Content="Orders"      Icon="Document" Tag="orders"    />
    </ui:NavigationView.MenuItems>
    <Frame x:Name="ContentFrame" />
</ui:NavigationView>

<!-- InfoBar — in-app notification banner -->
<ui:InfoBar Title="Save successful"
            Message="Customer record was saved."
            Severity="Success"
            IsOpen="{Binding ShowSavedBanner}" />

<!-- NumberBox -->
<ui:NumberBox Value="{Binding Quantity, Mode=TwoWay}"
              Minimum="1" Maximum="9999"
              SpinButtonMode="Compact" />

<!-- CommandBar -->
<ui:CommandBar>
    <ui:CommandBarButton Icon="Save"   Label="Save"   Command="{Binding SaveCommand}" />
    <ui:CommandBarButton Icon="Delete" Label="Delete" Command="{Binding DeleteCommand}" />
    <ui:CommandBarSeparator />
    <ui:CommandBarButton Icon="Refresh" Label="Refresh" Command="{Binding RefreshCommand}" />
</ui:CommandBar>

<!-- SplitButton -->
<ui:SplitButton Content="New Customer" Command="{Binding NewCommand}">
    <ui:SplitButton.Flyout>
        <MenuFlyout>
            <MenuItem Header="From Template" Command="{Binding NewFromTemplateCommand}" />
            <MenuItem Header="Import CSV"    Command="{Binding ImportCommand}" />
        </MenuFlyout>
    </ui:SplitButton.Flyout>
</ui:SplitButton>
```

**Control inventory (by category):**

| Category | Controls |
|----------|---------|
| Navigation | `NavigationView`, `NavigationViewItem`, `NavigationViewItemGroup`, `NavigationFrameControl`, `BreadcrumbBarControl`, `NavigationPage` |
| Commanding | `CommandBarControl`, `CommandBarButton`, `CommandBarFlyout`, `CommandBarGroup`, `CommandBarSpacer`, `DropDownButton`, `SplitButton` |
| Notifications | `InfoBar`, `InfoBadge`, `ToastNotification` |
| Input | `NumberBox`, `AutoSuggestBox`, `ColorPickerEditor`, `RatingEditor`, `FontFamilyDropdownEditor`, `FontWeightSlider`, `GradientEditor`, `BorderEditorControl`, `CornerRadiusEditor`, `OpacitySliderEditor`, `ShadowEditor`, `NumericSpinnerEditor` |
| Layout | `FlowLayout`, `StackLayout`, `SplitPanel`, `OverflowWrapPanel`, `ItemsRepeater` |
| Status / Progress | `StatusBarControl`, `StatusBarItemControl`, `ProgressRing`, `StepProgressBar`, `StepProgressItem` |
| Windowing | `FlyoutContentControl`, `FlyoutLayoutControl`, wizard infrastructure |
| Property Editing | `PropertyPanelHost`, `PropertySectionControl`, `MarginPaddingDiagramEditor`, `PositionPickerEditor` |
| Identity | `PersonPicture`, `LoginControl` |
| Paging | `PipsPager` |
| Icons | `FontIcon`, `PathIcon`, `AnimatedIcon` |
| Misc | `CalendarView`, `BreadcrumbItem`, `StyleToggleButtonGroup`, `AlignmentButtonGroup`, `SyntaxEditorControl`, `MeasurementHudControl`, `DragGuidesOverlay`, `ElementManipulatorControl` |

---

### 27. `Invitrek.Foundation.Presentation.Docking.Contracts`

**Assembly:** `Invitrek.Foundation.Presentation.Docking.Contracts`  
**Project:** `src/UIFramework/Docking.Contracts/Presentation.Docking.Contracts.csproj`  
**Targets:** `net48` + `net10.0-windows`

Platform-agnostic docking framework contracts. Reference this package from ViewModels and shell modules — never depend on the concrete `Docking` implementation.

```csharp
// Namespace: Invitrek.Foundation.Presentation.Docking

// Register a tool window (from a shell module):
[ToolWindow(Id = "00000000-0000-0000-0000-000000000001", DefaultSide = DockSide.Left)]
public class SolutionExplorerToolWindow : IDockToolWindow
{
    public string Title => "Solution Explorer";
    public UIElement Content => new SolutionExplorerView();
}

// Show/hide from ViewModel:
_dockManager.Show(SolutionExplorerToolWindow.Id);
_dockManager.Hide(SolutionExplorerToolWindow.Id);

// Save/restore layout:
_dockManager.SaveLayout("default");
_dockManager.RestoreLayout("default");
```

Key types: `IDockManager`, `IDockToolWindow`, `IDockDocumentWindow`, `IDockLayoutStore`, `IDockNamedLayoutStore`, `IDockToolWindowRegistry`, `ToolWindowAttribute`, `DockSide`, `DockLayout`.

---

### 28. `Invitrek.Foundation.Presentation.Docking`

**Assembly:** `Invitrek.Foundation.Presentation.Docking`  
**Project:** `src/UIFramework/Docking/Presentation.Docking.csproj`  
**Targets:** `net48` + `net10.0-windows`

Concrete WPF docking framework implementation. Provides the multi-pane shell, tool window host, layout persistence, and `IDockManager` implementation. Register via DI; consumers code against `IDockManager` (§27) only.

```csharp
// DI registration (host startup):
services.AddSingleton<IDockManager, WpfDockManager>();
```

---

### 29. `Invitrek.Foundation.Presentation.CodeEditor`

**Assembly:** `Invitrek.Foundation.Presentation.CodeEditor`  
**Project:** `src/UIFramework/CodeEditor/Presentation.CodeEditor.csproj`  
**Targets:** `net48` + `net10.0-windows`  
**Used by:** DeveloperStudio, Html5Designer.

Syntax-highlighted WPF code editor control with language-aware tokenization, IntelliSense hook, code completion, brace matching, and theme support.

```xml
<!-- XAML usage -->
<editor:CodeEditorControl x:Name="Editor"
                           Language="{x:Static editor:CSharpLanguage.Instance}"
                           Text="{Binding SourceCode, Mode=TwoWay}"
                           Theme="Dark"
                           FontFamily="Cascadia Code"
                           FontSize="13" />
```

```csharp
// Provide custom IntelliSense:
Editor.IntelliSenseProvider = new MyIntelliSenseProvider();

// Handle completions:
Editor.CompletionCommitted += (s, e) =>
    StatusBar.Text = $"Completed: {e.Item.Label}";
```

Supported languages out of the box: `CSharpLanguage`, `CLanguage`, `CppLanguage`, `CssLanguage`, `SyntaxLanguage` (extensible base for custom languages).

Key types: `CodeEditorControl`, `SyntaxEditorControl`, `SyntaxHighlighter`, `SyntaxLanguage`, `IIntelliSenseProvider`, `CompletionItem`, `CompletionItemKind`, `CodeEditorThemeKeys`.

---

### 30. `Invitrek.Foundation.Presentation.DesignerCanvas`

**Assembly:** `Invitrek.Foundation.Presentation.DesignerCanvas`  
**Project:** `src/UIFramework/DesignerCanvas/Presentation.DesignerCanvas.csproj`  
**Targets:** `net48` + `net10.0-windows`  
**Used by:** Html5Designer, visual design tools.

Interactive WPF design-surface canvas for building visual designers and diagram tools. Supports element drag/resize/align, snapping, auto-layout algorithms, undo/redo via command objects, canvas size presets, and real-time multi-user collaboration events.

```csharp
// Add an element to the canvas:
_canvas.ExecuteCommand(new AddCanvasElementCommand(element, position));

// Align selected elements:
_canvas.Align(AlignmentSuggestion.Left);

// Auto-layout:
_canvas.ApplyLayout(AutoLayoutAlgorithm.Hierarchical);

// Collaboration — broadcast cursor position to other users:
_canvas.CollaboratorCursorMoved += (s, e) =>
    _hub.BroadcastCursor(e.CollaboratorId, e.Position);
```

Key types: `IDesignerCanvas`, `AddCanvasElementCommand`, `CompositeCanvasCommand`, `AlignmentSuggestion`, `AutoLayoutAlgorithm`, `CanvasMode`, `CanvasSizePresetRegistry`, `CollaboratorInfo`, collaboration event args.

---

### 31. `Invitrek.Foundation.TaskDialogs`

**Assembly:** `Invitrek.Foundation.TaskDialogs`  
**Project:** `src/UIFramework/TaskDialogs/Invitrek.Foundation.TaskDialogs.csproj`  
**Targets:** `net48` + `net10.0-windows`  
**Required by:** All WPF desktop projects.

Fluent Windows-style task dialog builder. Replaces raw `MessageBox` calls with rich, accessible dialogs that support icons, command links, progress, expandable details, and custom button sets.

```csharp
// Confirmation dialog:
var result = TaskDialogBuilder.Create()
    .WithTitle("Confirm Delete")
    .WithInstruction("Permanently delete this record?")
    .WithContent("This action cannot be undone.")
    .WithIcon(DialogIcon.Warning)
    .WithButtons(DialogButtons.YesNo)
    .Show();

if (result == DialogResult.Yes) { /* proceed */ }

// Progress dialog:
using var progress = TaskDialogBuilder.Create()
    .WithTitle("Importing...")
    .WithProgressBar(DialogProgressBarState.Normal)
    .ShowModeless();

for (int i = 0; i <= 100; i++)
{
    progress.SetProgress(i);
    await Task.Delay(20);
}
```

---

### 32. `Invitrek.Foundation.Localization`

**Assembly:** `Invitrek.Foundation.Localization`  
**Project:** `src/UIFramework/Localization/Localization.csproj`  
**Targets:** `net48` + `net10.0-windows`

UI localization helpers. Provides `MessageBoxHelper` and `MessageBoxDialogExtensions` for consistent, localizable message dialogs across all WPF projects.

```csharp
MessageBoxHelper.ShowError("Could not connect to server.", owner: this);
MessageBoxHelper.ShowInformation("Import completed successfully.", owner: this);

bool confirmed = MessageBoxHelper.Confirm("Save changes before closing?", owner: this);
```

---

### 33. `Invitrek.Foundation.Images`

**Assembly:** `Invitrek.Foundation.Images`  
**Project:** `src/UIFramework/Images/Invitrek.Foundation.Images.csproj`  
**Targets:** `net48` + `net10.0-windows`

Centralized image and icon resource library. All WPF projects reference icons from here to ensure visual consistency. Eliminates duplicated icon assets across domain projects.

```xml
<Image Source="{ui:ImageResource Icons/Save.png}" />
<Image Source="{ui:ImageResource Icons/Customer.png}" />
```

---

### 34. `Invitrek.Foundation.WindowsAPICodePack`

**Assembly:** `Invitrek.Foundation.WindowsAPICodePack`  
**Project:** `src/UIFramework/WindowsAPICodePack/Invitrek.Foundation.WindowsAPICodePack.csproj`  
**Targets:** `net48` + `net10.0-windows`

Windows Shell integration — enhanced file dialogs (Common Item Dialog), taskbar progress/overlays, jump lists, extended linguistic services, and sensor APIs. Wraps the Windows API Code Pack with a consistent managed API.

```csharp
// Common file dialog (much richer than OpenFileDialog):
using var dlg = new CommonOpenFileDialog
{
    Title           = "Select Project File",
    DefaultFileName = "*.csproj",
    Multiselect     = false
};
dlg.Filters.Add(new CommonFileDialogFilter("Project Files", "*.csproj"));

if (dlg.ShowDialog() == CommonFileDialogResult.Ok)
    OpenProject(dlg.FileName);

// Taskbar progress:
TaskbarManager.Instance.SetProgressValue(50, 100);
TaskbarManager.Instance.SetProgressState(TaskbarProgressBarState.Normal);
```

---

### 35. `Invitrek.Foundation.Windows.Forms`

**Assembly:** `Invitrek.Foundation.Windows.Forms`  
**Project:** `src/UIFramework/WinForms/Core/Invitrek.Foundation.Windows.Forms.csproj`  
**Targets:** `net48` + `net10.0-windows`

WinForms base controls and helpers for legacy WinForms integration within the platform. Used where WPF is not applicable (e.g., embedded tool windows hosted in legacy WinForms shells).

---

### 36. `Invitrek.Foundation.Drawing`

**Assembly:** `Invitrek.Foundation.Drawing`  
**Project:** `src/UIFramework/WinForms/Drawing/Invitrek.Foundation.Drawing.csproj`  
**Targets:** `net48` + `net10.0-windows`

GDI+ drawing helpers and extension methods. Used by WinForms controls and rendering code within UIFramework.

---

## Full Dependency Map

```
All domain projects
        │
        ├── Invitrek.Foundation.Core          ← mandatory (logging, DI, mapping, validation, ...)
        │
        ├── Invitrek.Foundation.Data          ← if DB access
        │   └── + one provider (SqlServer / PostgreSql / MySql / Sqlite / Oracle)
        │
        ├── Invitrek.Foundation.ServiceModel  ← if exposing or consuming services
        │   ├── + Hosting  (net48)  or  AspNetCore (net10)  ← host projects only
        │   └── + Proxy                                      ← client projects
        │
        ├── Invitrek.Foundation.Security      ← if auth/identity required
        │   └── + JWT / OAuth / Certificate provider
        │
        ├── Invitrek.Foundation.CodeModel     ← DevTools / code generators only
        │
        ├── Invitrek.Foundation.Caching.*     ← if caching required
        ├── Invitrek.Foundation.Logging.*     ← if structured logging sink needed
        ├── Invitrek.Foundation.Messaging.*   ← if message bus required
        ├── Invitrek.Foundation.Workflow      ← if workflow features required
        ├── Invitrek.Foundation.AI            ← if AI features required (planned)
        │
        └── UIFramework (desktop/UI projects only)
            ├── Invitrek.Foundation.UI                          ← mandatory for all UI projects
            ├── Invitrek.Foundation.Mvvm                        ← mandatory for all MVVM projects
            ├── Invitrek.Foundation.Mvvm.Extensions             ← entity CRUD ViewModels
            ├── Invitrek.Foundation.Presentation.Controls       ← WPF control suite
            ├── Invitrek.Foundation.Presentation.Docking.Contracts ← if dockable shell
            ├── Invitrek.Foundation.Presentation.Docking        ← shell host only
            ├── Invitrek.Foundation.Presentation.CodeEditor     ← if code editing required
            ├── Invitrek.Foundation.Presentation.DesignerCanvas ← if design canvas required
            ├── Invitrek.Foundation.TaskDialogs                 ← all WPF desktop projects
            ├── Invitrek.Foundation.Localization                ← all localized projects
            ├── Invitrek.Foundation.Images                      ← all WPF desktop projects
            └── Invitrek.Foundation.WindowsAPICodePack          ← if Windows Shell required
```

> **Note:** During pre-MVP active development all of the above are consumed as **direct project references**. Post-MVP they become **NuGet package references** (`Invitrek.Foundation.*`). See `architecture-guidelines.md §11.3`.
