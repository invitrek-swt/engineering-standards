# 🔐 Foundation Security Guide

> **Part of**: Foundation Libraries v2.0  
> **Package**: `Invitrek.Foundation.Core`  
> **Namespace**: `Invitrek.Foundation.Security`  
> **Last Updated**: December 2024

---

## 📋 Overview

The Foundation security abstractions provide a comprehensive set of interfaces and contracts for implementing cryptographic operations including hashing, encryption, password management, authentication, and authorization. These abstractions enable secure data handling with swappable implementations for different security providers.

**Key Features**:
- **Password Hashing** - Secure password storage with salt and iterations
- **Data Hashing** - SHA256, SHA512, MD5 hashing algorithms
- **Encryption/Decryption** - AES, RSA symmetric and asymmetric encryption
- **Authentication** - Token-based and credential-based authentication
- **Authorization** - Role-based and permission-based access control

---

## 🔑 Security Abstractions

### IHashingService

Provides cryptographic hashing operations for passwords and data integrity.

```csharp
public interface IHashingService
{
    // Hash operations
    string ComputeHash(string input);
    string ComputeHash(string input, string salt);
    byte[] ComputeHash(byte[] data);
    
    // Verification
    bool VerifyHash(string input, string hash);
    bool VerifyHash(string input, string salt, string hash);
    
    // Salt generation
    string GenerateSalt();
}
```

**Use Cases**:
- File integrity verification
- Data tampering detection
- Digital signatures
- Checksum generation

**Example Implementation**: SHA256HashingService

---

### IEncryptionService

Provides symmetric encryption and decryption for sensitive data.

```csharp
public interface IEncryptionService
{
    // String encryption
    string Encrypt(string plainText, string key);
    string Decrypt(string cipherText, string key);
    
    // Binary encryption
    byte[] Encrypt(byte[] data, string key);
    byte[] Decrypt(byte[] encryptedData, string key);
    
    // Key/IV generation
    string GenerateKey(int keySize = 256);
    string GenerateIV();
}
```

**Use Cases**:
- Connection string encryption
- API key protection
- Sensitive configuration data
- Personal data (PII) encryption
- Database field-level encryption

**Example Implementation**: AesEncryptionService (AES-256)

---

### IPasswordHasher

Specialized interface for password hashing with automatic salt management.

```csharp
public interface IPasswordHasher
{
    // Password operations
    string HashPassword(string password);
    bool VerifyPassword(string password, string hashedPassword);
}
```

**Use Cases**:
- User password storage
- Authentication systems
- Password change operations
- Credential verification

**Example Implementation**: Pbkdf2PasswordHasher (PBKDF2 with 10,000 iterations)

**Best Practices**:
- Never store plain-text passwords
- Always use salt (handled automatically)
- Use high iteration count (10,000+)
- Consider memory-hard algorithms (bcrypt, Argon2) for critical systems

---

### IAuthenticationService

Provides user authentication operations.

```csharp
public interface IAuthenticationService
{
    Task<AuthenticationResult> AuthenticateAsync(string username, string password, CancellationToken cancellationToken = default);
    Task<AuthenticationResult> AuthenticateAsync(string token, CancellationToken cancellationToken = default);
    Task SignOutAsync(CancellationToken cancellationToken = default);
}
```

**Authentication Result**:
```csharp
public class AuthenticationResult
{
    public bool IsAuthenticated { get; set; }
    public string Token { get; set; }
    public DateTime ExpiresAt { get; set; }
    public Dictionary<string, string> Claims { get; set; }
    public string ErrorMessage { get; set; }
}
```

---

### IAuthorizationService

Provides authorization and permission checking.

```csharp
public interface IAuthorizationService
{
    Task<bool> IsAuthorizedAsync(string resource, string action, CancellationToken cancellationToken = default);
    Task<IEnumerable<string>> GetPermissionsAsync(CancellationToken cancellationToken = default);
}
```

---

## 💡 Implementation Examples

### SHA256 Hashing Service

```csharp
public class Sha256HashingService : IHashingService
{
    public string ComputeHash(string input)
    {
        if (string.IsNullOrEmpty(input))
            throw new ArgumentNullException(nameof(input));

        using (var sha256 = System.Security.Cryptography.SHA256.Create())
        {
            var bytes = System.Text.Encoding.UTF8.GetBytes(input);
            var hash = sha256.ComputeHash(bytes);
            return BitConverter.ToString(hash).Replace("-", "").ToLowerInvariant();
        }
    }

    public string ComputeHash(string input, string salt)
    {
        return ComputeHash(input + salt);
    }

    public byte[] ComputeHash(byte[] data)
    {
        using (var sha256 = System.Security.Cryptography.SHA256.Create())
        {
            return sha256.ComputeHash(data);
        }
    }

    public bool VerifyHash(string input, string hash)
    {
        var computedHash = ComputeHash(input);
        return string.Equals(computedHash, hash, StringComparison.OrdinalIgnoreCase);
    }

    public bool VerifyHash(string input, string salt, string hash)
    {
        var computedHash = ComputeHash(input, salt);
        return string.Equals(computedHash, hash, StringComparison.OrdinalIgnoreCase);
    }

    public string GenerateSalt()
    {
        using (var rng = System.Security.Cryptography.RandomNumberGenerator.Create())
        {
            var salt = new byte[32];
            rng.GetBytes(salt);
            return BitConverter.ToString(salt).Replace("-", "").ToLowerInvariant();
        }
    }
}
```

**Usage**:
```csharp
var hashingService = new Sha256HashingService();

// Hash a string
var hash = hashingService.ComputeHash("mydata");
Console.WriteLine($"Hash: {hash}");

// Hash with salt
var salt = hashingService.GenerateSalt();
var saltedHash = hashingService.ComputeHash("mydata", salt);
Console.WriteLine($"Salted Hash: {saltedHash}");

// Verify hash
var isValid = hashingService.VerifyHash("mydata", salt, saltedHash);
Console.WriteLine($"Valid: {isValid}");
```

---

### AES Encryption Service

```csharp
public class AesEncryptionService : IEncryptionService
{
    private const int KeySize = 256;
    private const int BlockSize = 128;

    public string Encrypt(string plainText, string key)
    {
        var plainBytes = System.Text.Encoding.UTF8.GetBytes(plainText);
        var encryptedBytes = Encrypt(plainBytes, key);
        return Convert.ToBase64String(encryptedBytes);
    }

    public string Decrypt(string cipherText, string key)
    {
        var cipherBytes = Convert.FromBase64String(cipherText);
        var decryptedBytes = Decrypt(cipherBytes, key);
        return System.Text.Encoding.UTF8.GetString(decryptedBytes);
    }

    public byte[] Encrypt(byte[] data, string key)
    {
        using (var aes = System.Security.Cryptography.Aes.Create())
        {
            aes.KeySize = KeySize;
            aes.BlockSize = BlockSize;
            aes.Key = DeriveKey(key);
            aes.GenerateIV();

            using (var encryptor = aes.CreateEncryptor())
            using (var ms = new System.IO.MemoryStream())
            {
                // Write IV to the beginning
                ms.Write(aes.IV, 0, aes.IV.Length);

                using (var cs = new System.Security.Cryptography.CryptoStream(ms, encryptor, System.Security.Cryptography.CryptoStreamMode.Write))
                {
                    cs.Write(data, 0, data.Length);
                    cs.FlushFinalBlock();
                }

                return ms.ToArray();
            }
        }
    }

    public byte[] Decrypt(byte[] encryptedData, string key)
    {
        using (var aes = System.Security.Cryptography.Aes.Create())
        {
            aes.KeySize = KeySize;
            aes.BlockSize = BlockSize;
            aes.Key = DeriveKey(key);

            // Extract IV from the beginning
            var iv = new byte[aes.BlockSize / 8];
            Array.Copy(encryptedData, 0, iv, 0, iv.Length);
            aes.IV = iv;

            using (var decryptor = aes.CreateDecryptor())
            using (var ms = new System.IO.MemoryStream())
            {
                using (var cs = new System.Security.Cryptography.CryptoStream(ms, decryptor, System.Security.Cryptography.CryptoStreamMode.Write))
                {
                    cs.Write(encryptedData, iv.Length, encryptedData.Length - iv.Length);
                    cs.FlushFinalBlock();
                }

                return ms.ToArray();
            }
        }
    }

    public string GenerateKey(int keySize = 256)
    {
        using (var aes = System.Security.Cryptography.Aes.Create())
        {
            aes.KeySize = keySize;
            aes.GenerateKey();
            return Convert.ToBase64String(aes.Key);
        }
    }

    public string GenerateIV()
    {
        using (var aes = System.Security.Cryptography.Aes.Create())
        {
            aes.GenerateIV();
            return Convert.ToBase64String(aes.IV);
        }
    }

    private byte[] DeriveKey(string password)
    {
        using (var derive = new System.Security.Cryptography.Rfc2898DeriveBytes(
            password,
            System.Text.Encoding.UTF8.GetBytes("InvitrekSalt2024"),
            10000,
            System.Security.Cryptography.HashAlgorithmName.SHA256))
        {
            return derive.GetBytes(KeySize / 8);
        }
    }
}
```

**Usage**:
```csharp
var encryptionService = new AesEncryptionService();
var key = "MySecureKey123!@#";

// Encrypt string
var plainText = "Sensitive Data";
var encrypted = encryptionService.Encrypt(plainText, key);
Console.WriteLine($"Encrypted: {encrypted}");

// Decrypt string
var decrypted = encryptionService.Decrypt(encrypted, key);
Console.WriteLine($"Decrypted: {decrypted}");

// Generate key
var generatedKey = encryptionService.GenerateKey(256);
Console.WriteLine($"Generated Key: {generatedKey}");
```

---

### PBKDF2 Password Hasher

```csharp
public class Pbkdf2PasswordHasher : IPasswordHasher
{
    private const int SaltSize = 32;
    private const int HashSize = 32;
    private const int Iterations = 10000;

    public string HashPassword(string password)
    {
        if (string.IsNullOrEmpty(password))
            throw new ArgumentNullException(nameof(password));

        using (var rng = System.Security.Cryptography.RandomNumberGenerator.Create())
        {
            var salt = new byte[SaltSize];
            rng.GetBytes(salt);

            using (var pbkdf2 = new System.Security.Cryptography.Rfc2898DeriveBytes(
                password, salt, Iterations, System.Security.Cryptography.HashAlgorithmName.SHA256))
            {
                var hash = pbkdf2.GetBytes(HashSize);

                // Combine salt and hash
                var combined = new byte[SaltSize + HashSize];
                Array.Copy(salt, 0, combined, 0, SaltSize);
                Array.Copy(hash, 0, combined, SaltSize, HashSize);

                return Convert.ToBase64String(combined);
            }
        }
    }

    public bool VerifyPassword(string password, string hashedPassword)
    {
        var combined = Convert.FromBase64String(hashedPassword);

        if (combined.Length != SaltSize + HashSize)
            return false;

        var salt = new byte[SaltSize];
        var storedHash = new byte[HashSize];
        Array.Copy(combined, 0, salt, 0, SaltSize);
        Array.Copy(combined, SaltSize, storedHash, 0, HashSize);

        using (var pbkdf2 = new System.Security.Cryptography.Rfc2898DeriveBytes(
            password, salt, Iterations, System.Security.Cryptography.HashAlgorithmName.SHA256))
        {
            var computedHash = pbkdf2.GetBytes(HashSize);

            for (int i = 0; i < HashSize; i++)
            {
                if (storedHash[i] != computedHash[i])
                    return false;
            }

            return true;
        }
    }
}
```

**Usage**:
```csharp
var passwordHasher = new Pbkdf2PasswordHasher();

// Hash password
var password = "MySecurePassword123!";
var hashedPassword = passwordHasher.HashPassword(password);
Console.WriteLine($"Hashed Password: {hashedPassword}");

// Verify password
var isValid = passwordHasher.VerifyPassword(password, hashedPassword);
Console.WriteLine($"Password Valid: {isValid}");

// Wrong password
var wrongPassword = "WrongPassword";
var isInvalid = passwordHasher.VerifyPassword(wrongPassword, hashedPassword);
Console.WriteLine($"Wrong Password Valid: {isInvalid}"); // False
```

---

## 🎯 Usage in ApplicationBase

### Configuration

```csharp
public class SecuritySettings
{
    public string EncryptionKey { get; set; }
    public string HashingAlgorithm { get; set; } = "SHA256";
    public int PasswordHashIterations { get; set; } = 10000;
    public bool RequireHttps { get; set; } = true;
}

public class AppConfiguration
{
    public SecuritySettings Security { get; set; }
}
```

### Service Registration

```csharp
protected override void ConfigureServices(IServiceRegistry services)
{
    // Register security services
    services.Register<IHashingService, Sha256HashingService>(ServiceLifetime.Singleton);
    services.Register<IEncryptionService, AesEncryptionService>(ServiceLifetime.Singleton);
    services.Register<IPasswordHasher, Pbkdf2PasswordHasher>(ServiceLifetime.Singleton);
}
```

### Usage in Services

```csharp
public class UserService
{
    private readonly IPasswordHasher _passwordHasher;
    private readonly IEncryptionService _encryptionService;
    private readonly ILogger _logger;

    public UserService(IPasswordHasher passwordHasher, IEncryptionService encryptionService, ILogger logger)
    {
        _passwordHasher = passwordHasher;
        _encryptionService = encryptionService;
        _logger = logger;
    }

    public async Task<User> CreateUserAsync(string username, string password, string email)
    {
        // Hash password
        var passwordHash = _passwordHasher.HashPassword(password);

        // Encrypt email (PII)
        var encryptedEmail = _encryptionService.Encrypt(email, Configuration.Security.EncryptionKey);

        var user = new User
        {
            Username = username,
            PasswordHash = passwordHash,
            EncryptedEmail = encryptedEmail,
            CreatedAt = DateTime.UtcNow
        };

        await _repository.AddAsync(user);
        _logger.LogInfo($"User created: {username}");

        return user;
    }

    public async Task<User> AuthenticateAsync(string username, string password)
    {
        var user = await _repository.FindAsync(u => u.Username == username).FirstOrDefaultAsync();

        if (user == null)
        {
            _logger.LogWarning($"User not found: {username}");
            return null;
        }

        if (!_passwordHasher.VerifyPassword(password, user.PasswordHash))
        {
            _logger.LogWarning($"Invalid password for user: {username}");
            return null;
        }

        _logger.LogInfo($"User authenticated: {username}");
        return user;
    }
}
```

---

## 🔒 Security Best Practices

### Password Security

✅ **DO**:
- Use `IPasswordHasher` for password hashing (automatic salt management)
- Use high iteration counts (10,000+ for PBKDF2)
- Consider memory-hard algorithms (bcrypt, Argon2) for critical systems
- Implement password complexity requirements
- Store only hashed passwords, never plain text
- Use timing-safe comparison for password verification

❌ **DON'T**:
- Store plain-text passwords
- Use MD5 or SHA1 for password hashing
- Hard-code passwords or keys in source code
- Log passwords or sensitive data
- Use weak iteration counts (<1,000)

### Encryption Security

✅ **DO**:
- Use AES-256 for symmetric encryption
- Generate random IVs for each encryption
- Use PBKDF2 for key derivation from passwords
- Store encryption keys securely (Azure Key Vault, AWS KMS)
- Rotate encryption keys periodically
- Use authenticated encryption (GCM mode) when available

❌ **DON'T**:
- Reuse IVs with the same key
- Hard-code encryption keys
- Use ECB mode (insecure)
- Use weak key sizes (<128 bits)
- Store keys alongside encrypted data

### Hashing Security

✅ **DO**:
- Use SHA-256 or stronger (SHA-512)
- Always use salt for password hashing
- Use cryptographically secure random number generators
- Implement rate limiting for hash verification
- Use constant-time comparison for hash verification

❌ **DON'T**:
- Use MD5 or SHA-1 (broken algorithms)
- Hash passwords without salt
- Use predictable salts
- Allow unlimited verification attempts

---

## 🛡️ Common Security Scenarios

### Scenario 1: User Registration

```csharp
public async Task<User> RegisterUserAsync(string username, string password, string email)
{
    // 1. Validate inputs
    if (string.IsNullOrWhiteSpace(username))
        throw new ArgumentException("Username is required");
    
    if (password.Length < 8)
        throw new ArgumentException("Password must be at least 8 characters");

    // 2. Hash password
    var passwordHash = _passwordHasher.HashPassword(password);

    // 3. Encrypt email (PII)
    var encryptedEmail = _encryptionService.Encrypt(email, _configuration.Security.EncryptionKey);

    // 4. Generate verification token
    var verificationToken = _hashingService.GenerateSalt();

    // 5. Create user
    var user = new User
    {
        Username = username,
        PasswordHash = passwordHash,
        EncryptedEmail = encryptedEmail,
        VerificationToken = verificationToken,
        CreatedAt = DateTime.UtcNow
    };

    await _userRepository.AddAsync(user);

    // 6. Send verification email
    await SendVerificationEmailAsync(email, verificationToken);

    return user;
}
```

### Scenario 2: User Authentication with Caching

```csharp
public async Task<AuthenticationResult> AuthenticateAsync(string username, string password)
{
    // 1. Check cache
    var cacheKey = $"user:{username}";
    var cachedUser = _cache.Get<User>(cacheKey);

    if (cachedUser != null)
    {
        if (_passwordHasher.VerifyPassword(password, cachedUser.PasswordHash))
        {
            return new AuthenticationResult
            {
                IsAuthenticated = true,
                Token = GenerateJwtToken(cachedUser),
                ExpiresAt = DateTime.UtcNow.AddHours(1)
            };
        }
    }

    // 2. Query database
    var user = await _userRepository.FindAsync(u => u.Username == username).FirstOrDefaultAsync();

    if (user == null)
    {
        return new AuthenticationResult
        {
            IsAuthenticated = false,
            ErrorMessage = "Invalid username or password"
        };
    }

    // 3. Verify password
    if (!_passwordHasher.VerifyPassword(password, user.PasswordHash))
    {
        // Log failed attempt
        await LogFailedLoginAttemptAsync(username);

        return new AuthenticationResult
        {
            IsAuthenticated = false,
            ErrorMessage = "Invalid username or password"
        };
    }

    // 4. Update last login
    user.LastLoginDate = DateTime.UtcNow;
    await _userRepository.UpdateAsync(user);

    // 5. Cache user
    _cache.Set(cacheKey, user, TimeSpan.FromMinutes(30));

    // 6. Generate token
    return new AuthenticationResult
    {
        IsAuthenticated = true,
        Token = GenerateJwtToken(user),
        ExpiresAt = DateTime.UtcNow.AddHours(1)
    };
}
```

### Scenario 3: Sensitive Data Encryption

```csharp
public async Task<Order> CreateOrderAsync(Order order)
{
    // Encrypt sensitive fields
    order.CreditCardNumber = _encryptionService.Encrypt(
        order.CreditCardNumber,
        _configuration.Security.EncryptionKey);

    order.SSN = _encryptionService.Encrypt(
        order.SSN,
        _configuration.Security.EncryptionKey);

    // Hash for search purposes
    var creditCardHash = _hashingService.ComputeHash(order.CreditCardNumber);
    order.CreditCardHash = creditCardHash;

    await _orderRepository.AddAsync(order);

    return order;
}

public async Task<Order> GetOrderAsync(int orderId)
{
    var order = await _orderRepository.GetByIdAsync(orderId);

    // Decrypt sensitive fields
    order.CreditCardNumber = _encryptionService.Decrypt(
        order.CreditCardNumber,
        _configuration.Security.EncryptionKey);

    order.SSN = _encryptionService.Decrypt(
        order.SSN,
        _configuration.Security.EncryptionKey);

    return order;
}
```

---

## 📚 Related Documentation

- [Foundation Libraries Reference](FOUNDATION_LIBRARIES.md) - Complete API reference
- [Application Base Guide](APPLICATION_BASE_GUIDE.md) - Application framework guide
- [Foundation Facade Guide](FOUNDATION_FACADE_GUIDE.md) - Facade pattern implementation

---

## © License

© 2024 Invitrek Technologies. All rights reserved.  
Internal use only - not for public distribution.
