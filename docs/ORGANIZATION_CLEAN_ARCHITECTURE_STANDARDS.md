# Foundation Organization Standard: Clean Architecture Guidelines

## Overview

This document establishes **mandatory architectural standards** for all Invitrek Foundation libraries to ensure clean separation of concerns, proper dependency management, and adherence to SOLID principles.

---

## 🎯 Core Principles

### 1. **Dependency Inversion Principle (DIP)**

All Foundation libraries MUST follow the Dependency Inversion Principle:

- **Abstractions** (interfaces, attributes, contracts) belong in `Core`
- **Implementations** belong in separate feature libraries
- **Consuming code** depends on abstractions (Core), NOT implementations

### 2. **Separation of Concerns**

```
┌─────────────────────────────────────────────┐
│              Core Library                   │
│  • Interfaces (I*)                          │
│  • Attributes ([*Attribute])                │
│  • Abstract classes (Base*, Abstract*)      │
│  • Enums                                    │
│  • DTOs/Models (shared contracts)          │
└─────────────────────────────────────────────┘
            ▲                    ▲
            │ depends on         │ depends on
            │                    │
    ┌───────┴──────┐      ┌─────┴────────────┐
    │  Consumer    │      │  Implementation  │
    │  Library     │      │  Library         │
    │  (Data,      │      │  (Telemetry,     │
    │   Runtime)   │      │   Caching.Redis) │
    └──────────────┘      └──────────────────┘
```

---

## 📁 Standard Library Structure

### Core Library (`src/Core/`)

**MUST contain ONLY:**
- ✅ Interfaces (`I*.cs`)
- ✅ Attributes (`[*Attribute].cs`)
- ✅ Abstract base classes (`Base*.cs`, `Abstract*.cs`)
- ✅ Enums
- ✅ DTOs/Models (contracts shared across libraries)
- ✅ Exception types
- ✅ Constants

**MUST NOT contain:**
- ❌ Concrete implementations
- ❌ Provider-specific code
- ❌ External dependencies (NuGet packages)

**Folder Structure:**
```
src/Core/
├── Caching/
│   ├── ICacheProvider.cs
│   └── Attributes/
│       └── CacheableAttribute.cs
├── Logging/
│   ├── ILogger.cs
│   └── LogLevel.cs (enum)
├── Messaging/
│   ├── IMessageBus.cs
│   └── IMessageHandler.cs
├── Telemetry/
│   ├── ITelemetrySink.cs
│   └── Attributes/
│       ├── TelemetryInterceptorAttribute.cs
│       ├── TrackDuplicateDetectionAttribute.cs
│       └── TrackDataQualityAttribute.cs
└── Data/
    ├── IRepository.cs
    ├── IQueryRepository.cs
    └── ICommandRepository.cs
```

### Implementation Libraries (`src/<Feature>/`)

**MUST contain:**
- ✅ Concrete implementations of Core interfaces
- ✅ Provider-specific code
- ✅ External dependencies (NuGet packages)

**MUST:**
- ✅ Reference `Core` project
- ✅ Implement interfaces from Core
- ✅ Follow naming: `Invitrek.Foundation.<Feature>`

**Folder Structure:**
```
src/Telemetry/
├── OrmMetrics.cs (implements ITelemetrySink)
├── OrmMetricsSink.cs
├── NullTelemetrySink.cs
└── Interception/
    ├── TelemetryInterceptor.cs
    └── TelemetryConfiguration.cs

src/Caching.Redis/
├── RedisCacheProvider.cs (implements ICacheProvider)
└── RedisConfiguration.cs

src/Logging.Serilog/
├── SerilogLogger.cs (implements ILogger)
└── SerilogConfiguration.cs
```

### Consumer Libraries (`src/Data/`, `src/Runtime/`)

**MUST:**
- ✅ Reference `Core` ONLY (for abstractions)
- ❌ NOT reference implementation libraries (Telemetry, Caching.Redis, etc.)
- ✅ Accept interfaces via constructor (DI)
- ✅ Use Null Object Pattern where appropriate

---

## 🏗️ Implementation Pattern

### Pattern 1: Dependency Inversion with Interfaces

**Core Library:**
```csharp
// src/Core/Caching/ICacheProvider.cs
namespace Invitrek.Foundation.Caching
{
    public interface ICacheProvider
    {
        void Set(string key, object value, TimeSpan expiration);
        T Get<T>(string key);
        void Remove(string key);
    }
}
```

**Implementation Library:**
```csharp
// src/Caching.Redis/RedisCacheProvider.cs
using Invitrek.Foundation.Caching; // From Core
using StackExchange.Redis;

namespace Invitrek.Foundation.Caching.Redis
{
    public class RedisCacheProvider : ICacheProvider
    {
        private readonly IConnectionMultiplexer _redis;
        
        public void Set(string key, object value, TimeSpan expiration)
        {
            // Redis-specific implementation
        }
        
        public T Get<T>(string key)
        {
            // Redis-specific implementation
        }
    }
}
```

**Consumer Library:**
```csharp
// src/Data/BaseRepository.cs
using Invitrek.Foundation.Caching; // From Core (NOT Caching.Redis!)

namespace Invitrek.Foundation.Data
{
    public class BaseRepository<TEntity, TKey>
    {
        private readonly ICacheProvider _cache;
        
        public BaseRepository(ICacheProvider cache = null)
        {
            _cache = cache; // Null is OK - cache is optional
        }
        
        public async Task<TEntity> GetByIdAsync(TKey id)
        {
            // Try cache first (if available)
            if (_cache != null)
            {
                var cached = _cache.Get<TEntity>($"entity:{id}");
                if (cached != null) return cached;
            }
            
            // Load from database...
        }
    }
}
```

**Application (DI Configuration):**
```csharp
// References: Core, Caching.Redis (implementation)
using Invitrek.Foundation.Caching;
using Invitrek.Foundation.Caching.Redis;

services.AddScoped<ICacheProvider, RedisCacheProvider>();
```

---

## 📋 Checklist for New Libraries

When creating a new Foundation library:

### Step 1: Define Contracts in Core

- [ ] Create interface in `src/Core/<Feature>/I<Name>.cs`
- [ ] Create attributes (if needed) in `src/Core/<Feature>/Attributes/`
- [ ] Create enums (if needed) in `src/Core/<Feature>/`
- [ ] NO external dependencies in Core
- [ ] NO implementations in Core

### Step 2: Create Implementation Library

- [ ] Create project: `src/<Feature>/<Feature>.csproj`
- [ ] Add Core project reference
- [ ] Implement interfaces from Core
- [ ] Add external dependencies (NuGet) if needed
- [ ] Follow naming: `Invitrek.Foundation.<Feature>`

### Step 3: Update Consumer Libraries

- [ ] Consumer references Core ONLY
- [ ] Consumer accepts interface via constructor
- [ ] Use Null Object Pattern if optional
- [ ] NO direct reference to implementation library

### Step 4: Documentation

- [ ] Create `README.md` in implementation library
- [ ] Document interface usage
- [ ] Provide DI configuration examples
- [ ] Update organization architecture docs

---

## 🎓 Examples by Library Type

### Caching Libraries

**Core:**
```csharp
// src/Core/Caching/ICacheProvider.cs
namespace Invitrek.Foundation.Caching
{
    public interface ICacheProvider
    {
        void Set(string key, object value, TimeSpan expiration);
        T Get<T>(string key);
        void Remove(string key);
        void Clear();
    }
}
```

**Implementations:**
- `src/Caching.Memory/` → `MemoryCacheProvider : ICacheProvider`
- `src/Caching.Redis/` → `RedisCacheProvider : ICacheProvider`
- `src/Caching.Memcached/` → `MemcachedCacheProvider : ICacheProvider`

**Consumer:**
```csharp
// src/Data/BaseRepository.cs
private readonly ICacheProvider _cache; // From Core
```

---

### Logging Libraries

**Core:**
```csharp
// src/Core/Logging/ILogger.cs
namespace Invitrek.Foundation.Logging
{
    public interface ILogger
    {
        void Log(LogLevel level, string message, Exception exception = null);
        void LogDebug(string message);
        void LogInfo(string message);
        void LogWarning(string message);
        void LogError(string message, Exception exception = null);
    }
    
    public enum LogLevel
    {
        Debug, Info, Warning, Error, Critical
    }
}
```

**Implementations:**
- `src/Logging.Serilog/` → `SerilogLogger : ILogger`
- `src/Logging.NLog/` → `NLogLogger : ILogger`
- `src/Logging.Log4Net/` → `Log4NetLogger : ILogger`

**Consumer:**
```csharp
// src/Data/BaseRepository.cs
private readonly ILogger _logger; // From Core
```

---

### Messaging Libraries

**Core:**
```csharp
// src/Core/Messaging/IMessageBus.cs
namespace Invitrek.Foundation.Messaging
{
    public interface IMessageBus
    {
        Task PublishAsync<T>(T message) where T : class;
        void Subscribe<T>(Func<T, Task> handler) where T : class;
    }
}
```

**Implementations:**
- `src/Messaging.RabbitMQ/` → `RabbitMQMessageBus : IMessageBus`
- `src/Messaging.AzureServiceBus/` → `AzureServiceBusMessageBus : IMessageBus`
- `src/Messaging.InMemory/` → `InMemoryMessageBus : IMessageBus`

---

### Security Libraries

**Core:**
```csharp
// src/Core/Security/IAuthenticationProvider.cs
namespace Invitrek.Foundation.Security
{
    public interface IAuthenticationProvider
    {
        Task<AuthenticationResult> AuthenticateAsync(string username, string password);
        Task<bool> ValidateTokenAsync(string token);
    }
}
```

**Implementations:**
- `src/Security.JWT/` → `JwtAuthenticationProvider : IAuthenticationProvider`
- `src/Security.OAuth/` → `OAuthAuthenticationProvider : IAuthenticationProvider`

---

## 🚫 Anti-Patterns to Avoid

### ❌ Anti-Pattern 1: Implementation in Core

```csharp
// ❌ WRONG: Concrete implementation in Core
// src/Core/Caching/RedisCacheProvider.cs
namespace Invitrek.Foundation.Caching
{
    public class RedisCacheProvider : ICacheProvider
    {
        // This pollutes Core with Redis-specific code!
    }
}
```

**✅ CORRECT:**
```csharp
// ✅ Core: Interface only
// src/Core/Caching/ICacheProvider.cs

// ✅ Implementation in separate library
// src/Caching.Redis/RedisCacheProvider.cs
```

---

### ❌ Anti-Pattern 2: Consumer Depends on Implementation

```csharp
// ❌ WRONG: Data library references Caching.Redis
// src/Data/Data.csproj
<ItemGroup>
  <ProjectReference Include="..\Caching.Redis\Caching.Redis.csproj" />
</ItemGroup>

// ❌ WRONG: Direct reference to concrete class
using Invitrek.Foundation.Caching.Redis;

private readonly RedisCacheProvider _cache;
```

**✅ CORRECT:**
```csharp
// ✅ Data library references Core only
<ItemGroup>
  <ProjectReference Include="..\Core\Core.csproj" />
</ItemGroup>

// ✅ Depends on interface
using Invitrek.Foundation.Caching;

private readonly ICacheProvider _cache;
```

---

### ❌ Anti-Pattern 3: Attributes in Implementation Library

```csharp
// ❌ WRONG: Attribute in Telemetry library
// src/Telemetry/TelemetryInterceptorAttribute.cs
namespace Invitrek.Foundation.Telemetry
{
    [AttributeUsage(AttributeTargets.Class)]
    public class TelemetryInterceptorAttribute : Attribute { }
}
```

**✅ CORRECT:**
```csharp
// ✅ Attribute in Core (available everywhere)
// src/Core/Telemetry/Attributes/TelemetryInterceptorAttribute.cs
```

---

## 📊 Dependency Matrix

| Project | References | Exports |
|---------|-----------|---------|
| **Core** | None | Interfaces, Attributes, Enums |
| **Telemetry** | Core | Concrete implementations |
| **Caching.Redis** | Core | Concrete implementations |
| **Logging.Serilog** | Core, Serilog (NuGet) | Concrete implementations |
| **Data** | Core | Business logic using interfaces |
| **Runtime** | Core | Business logic using interfaces |
| **Application** | Core, Telemetry, Caching.Redis, etc. | DI configuration |

---

## 🔧 Migration Guide

### Migrating Existing Libraries

1. **Identify Abstractions**
   - Find interfaces, attributes, enums
   - Identify shared contracts

2. **Move to Core**
   - Move interfaces to `src/Core/<Feature>/`
   - Move attributes to `src/Core/<Feature>/Attributes/`
   - Move enums to `src/Core/<Feature>/`

3. **Update References**
   - Implementation library references Core
   - Consumer libraries reference Core ONLY
   - Remove circular dependencies

4. **Add Null Object Pattern**
   - Create `Null*` implementation (no-op)
   - Place in implementation library
   - Use in consumers: `_service ?? NullService.Instance`

5. **Update DI Configuration**
   - Application wires concrete implementations
   - Consumers receive interfaces

---

## ✅ Review Checklist

Before merging any PR:

- [ ] All interfaces in Core?
- [ ] All attributes in Core?
- [ ] No implementations in Core?
- [ ] Consumer libraries reference Core only?
- [ ] Implementation libraries implement Core interfaces?
- [ ] DI configuration in application layer?
- [ ] Null Object Pattern for optional dependencies?
- [ ] Documentation updated?
- [ ] All projects build successfully?

---

## 📚 Reference Architecture

### Telemetry (Reference Implementation)

**Core (`src/Core/Telemetry/`):**
- ✅ `ITelemetrySink.cs` - Interface
- ✅ `Attributes/TelemetryInterceptorAttribute.cs`
- ✅ `Attributes/TrackDuplicateDetectionAttribute.cs`

**Implementation (`src/Telemetry/`):**
- ✅ `OrmMetrics.cs` - Concrete implementation
- ✅ `OrmMetricsSink.cs` - ITelemetrySink implementation
- ✅ `NullTelemetrySink.cs` - Null Object Pattern

**Consumer (`src/Data/`):**
- ✅ References Core only
- ✅ Uses `ITelemetrySink` interface
- ✅ Injected via constructor

**Application:**
- ✅ References Core + Telemetry
- ✅ Configures DI: `services.AddScoped<ITelemetrySink, OrmMetricsSink>()`

---

## 🎯 Benefits

1. **Clean Dependencies**
   - No circular references
   - Clear dependency flow
   - Easy to understand

2. **Flexibility**
   - Swap implementations at runtime
   - Easy to add new providers
   - Optional features via DI

3. **Testability**
   - Mock interfaces easily
   - No dependencies on concrete implementations
   - Isolated unit tests

4. **Maintainability**
   - Changes isolated to implementation libraries
   - Consumers unaffected by implementation changes
   - Clear separation of concerns

5. **Extensibility**
   - New implementations without changing Core
   - Open/Closed Principle
   - Plugin architecture

---

## 📝 Enforcement

This architecture is **mandatory** for all Foundation libraries:

- ✅ New libraries MUST follow this pattern
- ✅ Existing libraries SHOULD be migrated
- ✅ Code reviews MUST enforce these guidelines
- ✅ CI/CD pipeline SHOULD validate dependencies

---

## 📖 Related Documents

- [Clean Architecture Summary](src/Core/Telemetry/CLEAN_ARCHITECTURE_SUMMARY.md)
- [Telemetry AOP Architecture](src/Telemetry/AOP_ARCHITECTURE.md)
- [Shared Project Pattern Guidelines](docs/ARCHITECTURE_GUIDELINES_SHARED_PROJECT_PATTERN.md)

---

**Version:** 1.0  
**Last Updated:** 2025  
**Status:** Mandatory for all Foundation projects

---

## License

MIT License - © 2024 Invitrek Technologies. All rights reserved.
