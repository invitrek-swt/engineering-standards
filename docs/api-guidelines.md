# API Guidelines — Invitrek-swt

**Applies to:** All service, REST, gRPC, and WCF contracts  
**Last updated:** See git history

---

## 1. API-First Rule (Mandatory)

- Define and approve the contract/API surface **before** implementation begins.
- Implementation must conform to approved contracts.
- Avoid contract-breaking changes during iterative optimization.
- Contracts are open for extension, closed for modification (Open/Closed Principle).

---

## 2. Service Contract Standards

- Service contracts shall be **async** (`Task` / `Task<T>` return types).
- Consumers depend only on `Services.Contracts` — never on implementation assemblies.
- Use DI to swap implementations (in-proc, REST proxy, WCF, gRPC, queues) without changing consumer code.

### 2.1 DataContract / DataMember (Mandatory for service DTOs)

DTOs used in service contract methods shall be decorated with `DataContract` and `DataMember(Order=N)` attributes for deterministic serialization and repeatable proxy/code generation:

```csharp
[DataContract]
public class CustomerRequest
{
    [DataMember(Order = 1)]
    public int CustomerId { get; set; }

    [DataMember(Order = 2)]
    public string Filter { get; set; }
}
```

### 2.2 Service contract example

```csharp
[ServiceContract]
public interface ICustomerService
{
    [OperationContract]
    Task<EntityListResult<Customer>> GetCustomerListAsync(CustomerListRequest request, CancellationToken token);

    [OperationContract]
    Task<EntityResult<Customer>> GetCustomerByIdAsync(CustomerRequest request, CancellationToken token);

    [OperationContract]
    Task<EntitySavedResult<Customer>> SaveCustomerAsync(EntitySaveRequest<Customer> request, CancellationToken token);

    [OperationContract]
    Task<EntityDeletedResult> DeleteCustomerAsync(EntityDeleteRequest<int> request, CancellationToken token);

    [OperationContract]
    Task<EntityPatchResult<Customer>> PatchCustomerAsync(PropertyChangedCollection request, CancellationToken token);
}
```

---

## 3. Multi-Protocol Support

When a service must be exposed over multiple protocols (WCF, gRPC, REST, SignalR):

- Use a **single, endpoint-agnostic service implementation**.
- Use a consistent middleware-like pipeline for cross-cutting concerns (logging, validation, auth).
- Protocol-specific wiring is configured at the host layer — not inside the service implementation.

---

## 4. DTO / Request Model Standards

- DTO/POCO request models may use validation attributes to make client input requirements explicit:
  - `[Required]`, `[DefaultValue]`, range and length constraints.
- Service entry points must enforce these validations before executing business logic.
- Default values must be deterministic and documented.
- Async (`Task`/`Task<T>`) signatures align with UI responsiveness and network I/O.
- In-proc implementations may wrap sync work with `Task.FromResult` / `Task.CompletedTask`.

---

## 5. Result Type Conventions

Use standard result types for consistent consumer experience:

| Operation | Result type |
|-----------|------------|
| List query | `EntityListResult<T>` |
| Single entity query | `EntityResult<T>` |
| Save (create/update) | `EntitySavedResult<T>` |
| Delete | `EntityDeletedResult` |
| Patch | `EntityPatchResult<T>` |

---

## 6. Namespace Conventions

| Layer | Namespace |
|-------|-----------|
| Service contracts | `Invitrek.[Domain].Services.Contracts` |
| Service implementations | `Invitrek.[Domain].Services` |
| Client-side proxies | `Invitrek.[Domain].Services.Proxy` |

---

## 7. Endpoint Convention (Attribute-Driven)

Use attribute-based endpoint suffix and protocol conventions:

```csharp
[ServiceEndpointSuffix("Customer")]
[ServiceEndpointProtocol(Protocol.Grpc | Protocol.SignalR)]
public interface ICustomerService : IServiceContract { ... }
```

Default endpoint naming follows `[Domain]/[Suffix]` convention; attribute overrides apply when explicit routing is required.
