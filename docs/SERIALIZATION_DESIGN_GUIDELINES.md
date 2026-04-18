# Serialization Design Guidelines

## Organizational Standard: Cross-Domain Reusability

**Core Principle:** Any class that may be used across domain boundaries (client/server, service boundaries, distributed systems) MUST be properly serializable using `DataContract` and `DataMember` attributes.

## Why DataContract/DataMember?

1. **Cross-Platform Compatibility:** Works with WCF, Web API, gRPC, and other service technologies
2. **Explicit Control:** Granular control over what gets serialized and in what order
3. **Version Tolerance:** Supports versioning through Order property
4. **Opt-In Model:** Only marked members are serialized (security)
5. **.NET Framework Compatibility:** Works with both .NET Framework 4.8 and modern .NET

## Mandatory Serialization Pattern

### 1. Class-Level Attributes

**Always use both attributes:**
```csharp
[Serializable]           // Binary serialization support
[DataContract]           // DataContract serialization (WCF, JSON, XML)
public class YourClass
{
    // ...
}
```

**Why both?**
- `[Serializable]` - Legacy binary serialization, some frameworks require it
- `[DataContract]` - Modern, explicit, version-tolerant serialization

### 2. Property-Level Attributes

**Mark all serializable properties with DataMember:**
```csharp
[DataMember(Order = 1)]
public int Id { get; set; }

[DataMember(Order = 2)]
public string Name { get; set; }

[DataMember(Order = 3)]
public DateTime CreatedDate { get; set; }
```

**Order Property:**
- Required for version tolerance
- Start at 1 and increment
- Maintain order across versions
- New properties get next available number

### 3. Computed Properties

**Mark computed properties with IgnoreDataMember:**
```csharp
/// <summary>
/// Gets the full name (computed from FirstName and LastName).
/// </summary>
/// <remarks>
/// This is a computed property, not serialized separately.
/// </remarks>
[IgnoreDataMember]
public string FullName => $"{FirstName} {LastName}";
```

### 4. Non-Serializable Members

**Mark with appropriate attributes:**
```csharp
[NonSerialized]                    // For fields
private object? _internalState;

[IgnoreDataMember]                 // For properties
public object LocalCache { get; set; }
```

## Complete Example: Auditing Classes

### AuditUser<TKey> - ID for Storage, Name for Display

**Design Rationale:**
This class demonstrates the **"ID for Storage, Name for Display"** pattern:
- **Id**: Stored in database as foreign key (system reference, 4-16 bytes)
- **Name**: Used for display only (human-readable, not stored in audit columns)

```csharp
[Serializable]
[DataContract]
public class AuditUser<TKey>
{
    /// <summary>
    /// Gets or sets the unique identifier of the user.
    /// </summary>
    /// <remarks>
    /// Stored in database tables as foreign key reference.
    /// Used for system operations, relational integrity, and queries.
    /// </remarks>
    [DataMember(Order = 1)]
    public TKey? Id { get; set; }

    /// <summary>
    /// Gets or sets the display name of the user.
    /// </summary>
    /// <remarks>
    /// Used for display purposes only, not stored in audit columns.
    /// Populated from user service/cache when entity is loaded.
    /// Serialized for client-side display without additional lookups.
    /// </remarks>
    [DataMember(Order = 2)]
    public string? Name { get; set; }

    // Parameterless constructor required for serialization
    public AuditUser()
    {
    }

    public AuditUser(TKey id, string name)
    {
        Id = id;
        Name = name;
    }
}
```

**Database Storage:**
```sql
-- Only ID stored (efficient, normalized)
CREATE TABLE Orders (
    OrderId INT PRIMARY KEY,
    CreatedUserId INT,      -- Foreign key only
    CreatedDate DATETIME,
    ModifiedUserId INT,     -- Foreign key only
    ModifiedDate DATETIME
);
```

**Entity Hydration Pattern:**
```csharp
// Load from database (only IDs)
var order = await _dbContext.Orders.FindAsync(orderId);

// Hydrate with names for display
order.CreatedUser = new AuditUser<int>(
    order.CreatedUserId,                           // From database column
    await _userService.GetUserNameAsync(order.CreatedUserId)  // From service/cache
);

// Serialize to client (both ID and Name included)
return order;  // Client has complete information for display
```

This pattern provides:
- **Efficient database storage** (only IDs)
- **Rich client experience** (names available)
- **Flexible hydration** (names from cache/service)
- **Complete serialization** (both values transfer)

### AuditableEntityReadOnly<TKey>
```csharp
[Serializable]
[DataContract]
public abstract class AuditableEntityReadOnly<TKey> : IAuditableEntityReadOnly<TKey>
{
    [DataMember(Order = 1)]
    public AuditUser<TKey>? CreatedUser { get; private set; }

    [DataMember(Order = 2)]
    public DateTime CreatedDate { get; private set; }

    protected AuditableEntityReadOnly()
    {
        CreatedDate = DateTime.UtcNow;
    }
}
```

### AuditableEntity<TKey>
```csharp
[Serializable]
[DataContract]
public abstract class AuditableEntity<TKey> : AuditableEntityReadOnly<TKey>, IAuditableEntity<TKey>
{
    [DataMember(Order = 3)]
    public AuditUser<TKey>? ModifiedUser { get; private set; }

    [DataMember(Order = 4)]
    public DateTime? ModifiedDate { get; private set; }

    // Computed property - not serialized
    [IgnoreDataMember]
    public bool IsModified => ModifiedDate.HasValue;
}
```

### ChangeTracker (Service-Oriented)
```csharp
[Serializable]
[DataContract]
public class ChangeTracker
{
    [DataMember(Order = 1)]
    private Dictionary<string, PropertyChange> _changes = new();

    [DataMember(Order = 2)]
    private Dictionary<string, object?> _originalValues = new();

    [DataMember(Order = 3)]
    private bool _isEnabled;

    // Entity reference not serialized (client attaches after deserialization)
    [NonSerialized]
    private object? _entity;

    [DataMember(Order = 4)]
    public bool IsEnabled => _isEnabled;

    // Computed property - not serialized
    [IgnoreDataMember]
    public bool HasChanges => _changes.Count > 0;

    [OnDeserialized]
    private void OnDeserialized(StreamingContext context)
    {
        // Initialize after deserialization
        _changes ??= new Dictionary<string, PropertyChange>();
        _originalValues ??= new Dictionary<string, object?>();
    }
}
```

## Special Considerations

### 1. Generic Types

Generics are fully supported:
```csharp
[Serializable]
[DataContract]
public class Container<T>
{
    [DataMember(Order = 1)]
    public T Value { get; set; }
}
```

### 2. Collections

Use serializable collection types:
```csharp
[DataMember(Order = 1)]
public List<string> Items { get; set; } = new();

[DataMember(Order = 2)]
public Dictionary<string, object> Properties { get; set; } = new();
```

### 3. Inheritance

Derived classes continue the Order sequence:
```csharp
[Serializable]
[DataContract]
public class BaseClass
{
    [DataMember(Order = 1)]
    public int Id { get; set; }
}

[Serializable]
[DataContract]
public class DerivedClass : BaseClass
{
    // Continue from 2
    [DataMember(Order = 2)]
    public string Name { get; set; }
}
```

### 4. Private Setters

Private setters are allowed and will serialize:
```csharp
[DataMember(Order = 1)]
public int Id { get; private set; }  // ✅ Will serialize
```

### 5. Interfaces

Interfaces don't need serialization attributes (implementations do):
```csharp
public interface IAuditableEntity<TKey>
{
    AuditUser<TKey>? CreatedUser { get; }
    DateTime CreatedDate { get; }
}

// Implementation needs attributes
[Serializable]
[DataContract]
public class MyEntity : IAuditableEntity<int>
{
    [DataMember(Order = 1)]
    public AuditUser<int>? CreatedUser { get; set; }

    [DataMember(Order = 2)]
    public DateTime CreatedDate { get; set; }
}
```

## Version Tolerance Guidelines

### Adding New Properties
```csharp
// Version 1
[Serializable]
[DataContract]
public class Customer
{
    [DataMember(Order = 1)]
    public int Id { get; set; }

    [DataMember(Order = 2)]
    public string Name { get; set; }
}

// Version 2 - Add new property
[Serializable]
[DataContract]
public class Customer
{
    [DataMember(Order = 1)]
    public int Id { get; set; }

    [DataMember(Order = 2)]
    public string Name { get; set; }

    // New in v2
    [DataMember(Order = 3)]
    public string Email { get; set; }
}
```

**Rules:**
- Never change existing Order values
- Never remove properties (mark as obsolete instead)
- New properties get next sequential Order
- Old clients will ignore new properties
- New clients will have default values for missing properties

## Testing Serialization

### Round-Trip Test Template
```csharp
[Fact]
public void Class_Should_Serialize_And_Deserialize()
{
    // Arrange
    var original = new AuditUser<int>
    {
        Id = 123,
        Name = "John Doe"
    };

    // Act - Serialize
    var serializer = new DataContractSerializer(typeof(AuditUser<int>));
    string xml;
    using (var stream = new MemoryStream())
    {
        serializer.WriteObject(stream, original);
        stream.Position = 0;
        using (var reader = new StreamReader(stream))
        {
            xml = reader.ReadToEnd();
        }
    }

    // Act - Deserialize
    AuditUser<int> deserialized;
    using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(xml)))
    {
        deserialized = (AuditUser<int>)serializer.ReadObject(stream);
    }

    // Assert
    deserialized.Should().NotBeNull();
    deserialized.Id.Should().Be(original.Id);
    deserialized.Name.Should().Be(original.Name);
}
```

### JSON Serialization Test
```csharp
[Fact]
public void Class_Should_Serialize_To_JSON()
{
    // Arrange
    var obj = new AuditableEntity<int> { /* ... */ };

    // Act
    var json = JsonSerializer.Serialize(obj);
    var deserialized = JsonSerializer.Deserialize<AuditableEntity<int>>(json);

    // Assert
    deserialized.Should().BeEquivalentTo(obj);
}
```

## Common Patterns in Foundation.Core

### Pattern 1: Entity Base Classes
```csharp
[Serializable]
[DataContract]
public abstract class AuditableEntityReadOnly<TKey> : IAuditableEntityReadOnly<TKey>
{
    [DataMember(Order = 1)]
    public AuditUser<TKey>? CreatedUser { get; private set; }

    [DataMember(Order = 2)]
    public DateTime CreatedDate { get; private set; }
}
```

### Pattern 2: Change Tracking
```csharp
[Serializable]
[DataContract]
public class ChangeTracker
{
    [DataMember(Order = 1)]
    private Dictionary<string, PropertyChange> _changes = new();

    [NonSerialized]
    private object? _entity;  // Not serialized - attach after deserialization
}
```

### Pattern 3: DTOs/ViewModels
```csharp
[Serializable]
[DataContract]
public class CustomerViewModel
{
    [DataMember(Order = 1)]
    public int Id { get; set; }

    [DataMember(Order = 2)]
    public string Name { get; set; }

    [DataMember(Order = 3)]
    public AuditUser<int> CreatedUser { get; set; }
}
```

## Checklist for New Classes

Before committing any class that crosses domain boundaries:

- [ ] Class has `[Serializable]` attribute
- [ ] Class has `[DataContract]` attribute
- [ ] All properties to be serialized have `[DataMember(Order = n)]`
- [ ] Order numbers start at 1 and increment sequentially
- [ ] Computed properties have `[IgnoreDataMember]`
- [ ] Non-serializable members have `[NonSerialized]` or `[IgnoreDataMember]`
- [ ] Parameterless constructor exists (public or protected)
- [ ] `[OnDeserialized]` method exists if initialization is needed
- [ ] Round-trip serialization test written
- [ ] Verified with both DataContractSerializer and JSON serialization

## Tools and Validation

### Visual Studio Code Analyzer
Consider creating a Roslyn analyzer to enforce:
- `[DataContract]` on classes intended for serialization
- `[DataMember(Order = n)]` on serializable properties
- Order sequence validation
- Parameterless constructor presence

### Documentation
- Always document serialization behavior in XML comments
- Note which properties are not serialized
- Explain computed properties

## References

- **Foundation.Core.Auditing** - Complete audit trail infrastructure (reference implementation)
- **Foundation.Core.ChangeTracking** - Property change tracking with serialization
- **CHANGE_TRACKING_SERVICE_ARCHITECTURE.md** - Client/server patterns
- **AUDITING_INFRASTRUCTURE.md** - Auditing design patterns

## Questions?

Contact the Architecture team for:
- Serialization design reviews
- Performance optimization
- Version migration strategies
- Custom serialization requirements

