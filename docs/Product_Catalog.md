# Invitrek Foundation - Product Catalog

**Organization:** Invitrek  
**Product Line:** Foundation Framework Suite  
**Date:** March 2, 2026  
**Status:** Active Development

---

## 📦 **COMPLETE PRODUCT PORTFOLIO**

### **1. Data-Access Framework**
**Product Name:** Data-Access Framework  
**Tagline:** "Enterprise-grade data access for .NET applications"  
**Target Audience:** Enterprise developers, database architects, backend engineers  
**License Type:** Commercial (Individual, Team, Professional, Enterprise)

**Components:**
- `Foundation.Data` - Core ORM and repository pattern
- `Foundation.Data.SqlServer` - SQL Server provider
- `Foundation.Data.Oracle` - Oracle provider
- `Foundation.Data.MySQL` - MySQL provider
- `Foundation.Data.PostgreSQL` - PostgreSQL provider
- `Foundation.Data.SQLite` - SQLite provider
- `Foundation.Data.Firebird` - Firebird provider
- `Foundation.Data.MariaDB` - MariaDB provider
- `Foundation.Data.DB2` - IBM DB2 provider
- `Foundation.Caching` - In-memory and distributed caching
- `Foundation.Pooling` - Connection pooling and resource management

**Key Features:**
- Multi-database support (9+ databases)
- High-performance ORM with repository pattern
- Change tracking and dirty checking
- Built-in caching with cache-aside pattern
- LINQ query support
- Transaction management
- Audit logging (created/modified timestamps)
- Soft delete support

**Deployment Model:**
- NuGet packages
- On-premises deployment
- Cloud-ready (Azure, AWS)

---

### **2. Communication Framework**
**Product Name:** Communication Framework  
**Tagline:** "Microservices infrastructure for .NET"  
**Target Audience:** Microservices architects, distributed systems engineers, backend teams  
**License Type:** Commercial (Team, Professional, Enterprise)

**Components:**
- `Foundation.ServiceModel` - WCF, gRPC, REST service hosting
- `Foundation.ServiceModel.WCF` - WCF service support
- `Foundation.ServiceModel.Grpc` - gRPC service support
- `Foundation.ServiceModel.AspNetCore` - ASP.NET Core integration
- `Foundation.Messaging` - Message-driven architecture
- `Foundation.Messaging.RabbitMQ` - RabbitMQ integration
- `Foundation.Messaging.Kafka` - Apache Kafka integration
- `Foundation.ServiceDiscovery` - Service registry and load balancing
- `Foundation.Resilience` - Circuit breaker, retry, timeout patterns

**Key Features:**
- Service hosting (WCF, gRPC, REST)
- Message queuing (RabbitMQ, Kafka)
- Service discovery and load balancing
- Resilience patterns (circuit breaker, retry, bulkhead)
- API gateway capabilities
- Contract-first development
- Versioning support
- Observability (metrics, tracing)

**Deployment Model:**
- NuGet packages
- Docker containers
- Kubernetes-ready
- Cloud-native (Azure Service Fabric, AWS ECS)

---

### **3. Visual Studio** *(IDE Extension)*
**Product Name:** Invitrek Visual Studio Extension  
**Tagline:** "Productivity tools for Foundation Framework developers"  
**Target Audience:** .NET developers using Visual Studio  
**License Type:** Free (Community), Commercial (Professional, Enterprise)

**Components:**
- `Invitrek.VisualStudio.Extension` - Main extension package
- `Invitrek.VisualStudio.Templates` - Project/item templates
- `Invitrek.VisualStudio.CodeGen` - Code generation tools
- `Invitrek.VisualStudio.Analyzers` - Code analyzers and refactorings
- `Invitrek.VisualStudio.Debugging` - Enhanced debugging visualizers

**Key Features:**
- Foundation Framework project templates
- Code snippets and IntelliSense enhancements
- Visual designers (entity designer, service designer)
- Code generation (repositories, entities, services)
- Live templates for common patterns
- Integrated documentation browser
- NuGet package manager integration
- Database schema visualization
- Service contract designer

**Deployment Model:**
- Visual Studio Marketplace
- VSIX installer
- Auto-update via Visual Studio Extension Manager

**Supported Visual Studio Versions:**
- Visual Studio 2019 (16.x)
- Visual Studio 2022 (17.x)

---

### **4. Developer Studio** *(Standalone IDE)*
**Product Name:** Invitrek Developer Studio  
**Tagline:** "Purpose-built IDE for Foundation Framework development"  
**Target Audience:** Enterprise teams, full-stack developers, architects  
**License Type:** Commercial (Professional, Enterprise)

**Components:**
- `DeveloperStudio.Core` - Core IDE framework
- `DeveloperStudio.Editor` - Code editor (Monaco-based or custom)
- `DeveloperStudio.Designer` - Visual designers (entity, service, UI)
- `DeveloperStudio.Debugger` - Integrated debugger
- `DeveloperStudio.Database` - Database management tools
- `DeveloperStudio.Git` - Git integration
- `DeveloperStudio.Extensions` - Extension system
- `DeveloperStudio.Terminal` - Integrated terminal
- `DeveloperStudio.Profiler` - Performance profiling tools

**Key Features:**
- Purpose-built for Foundation Framework
- Unified experience (data, services, UI, identity)
- Visual entity relationship designer
- Service contract designer with live preview
- Database schema management (migrations, versioning)
- Built-in NuGet package manager
- Git workflow integration
- Performance profiler and analyzer
- Integrated testing tools (unit, integration)
- Theme support (Light, Dark, High Contrast)
- Plugin architecture for extensibility

**Deployment Model:**
- Standalone installer (Windows, macOS, Linux)
- Auto-update mechanism
- Offline activation support (Enterprise)
- Portable edition (no installation required)

**System Requirements:**
- Windows 10/11, macOS 10.15+, Linux (Ubuntu 20.04+)
- .NET 10 Runtime
- 4 GB RAM minimum, 8 GB recommended
- 2 GB disk space

---

### **5. Nexus Server**
**Product Name:** Nexus Server  
**Tagline:** "Centralized management platform for Foundation Framework applications"  
**Target Audience:** DevOps engineers, system administrators, enterprise IT  
**License Type:** Commercial (Server licensing: up to 10/50/unlimited apps)

**Components:**
- `Nexus.Server.Core` - Core server engine
- `Nexus.Server.Management` - Management API
- `Nexus.Server.Dashboard` - Web-based dashboard
- `Nexus.Server.Monitoring` - Application monitoring and health checks
- `Nexus.Server.Logging` - Centralized logging aggregation
- `Nexus.Server.Configuration` - Centralized configuration management
- `Nexus.Server.Deployment` - Application deployment orchestration
- `Nexus.Server.Licensing` - License management
- `Nexus.Server.Identity` - Integration with Identity Server
- `Nexus.Server.Analytics` - Usage analytics and reporting

**Key Features:**
- Centralized application management
- Real-time monitoring and alerting
- Centralized logging (Elasticsearch integration)
- Configuration management (per environment)
- Deployment orchestration (CI/CD integration)
- License management and enforcement
- User and role management
- Health checks and diagnostics
- Performance metrics (CPU, memory, requests/sec)
- Audit logging (who deployed what, when)
- Multi-tenancy support
- API-first design (REST + SignalR for real-time)

**Deployment Model:**
- On-premises (Windows Server, Linux)
- Docker containers
- Kubernetes Helm charts
- Cloud IaaS (Azure VMs, AWS EC2)
- High availability (active-passive, active-active)

**System Requirements:**
- Windows Server 2019+, Linux (Ubuntu 20.04+)
- .NET 10 Runtime
- SQL Server 2019+ or PostgreSQL 12+ (metadata storage)
- 8 GB RAM minimum, 16 GB recommended
- 10 GB disk space + storage for logs

---

### **6. Nexus Client**
**Product Name:** Nexus Client  
**Tagline:** "Desktop client for managing Foundation Framework applications"  
**Target Audience:** Developers, DevOps engineers, system administrators  
**License Type:** Free (comes with Nexus Server subscription)

**Components:**
- `Nexus.Client.Core` - Core client framework
- `Nexus.Client.UI` - WPF-based UI
- `Nexus.Client.Dashboard` - Application dashboard view
- `Nexus.Client.Deployment` - Deployment tools
- `Nexus.Client.Monitoring` - Monitoring and diagnostics
- `Nexus.Client.Configuration` - Configuration editor
- `Nexus.Client.Logs` - Log viewer and search
- `Nexus.Client.Terminal` - Remote terminal access
- `Nexus.Client.Scripting` - PowerShell/Python scripting

**Key Features:**
- Desktop application (Windows, macOS, Linux)
- Connect to one or more Nexus Servers
- Real-time dashboard (application health, metrics)
- Log viewer with filtering and search
- Configuration editor (multi-environment support)
- Deployment wizard (1-click deployments)
- Remote diagnostics (thread dumps, memory dumps)
- Performance profiling
- PowerShell/Python scripting for automation
- Offline mode (cached data)
- Dark/light theme support

**Deployment Model:**
- Desktop installer (Windows, macOS, Linux)
- Auto-update via Nexus Server
- Portable edition (no installation)

**System Requirements:**
- Windows 10/11, macOS 10.15+, Linux (Ubuntu 20.04+)
- .NET 10 Runtime
- 2 GB RAM
- 500 MB disk space

---

### **7. UI Framework**
**Product Name:** UI Framework  
**Tagline:** "Modern UI components for .NET desktop applications"  
**Target Audience:** Desktop application developers, UX designers  
**License Type:** Commercial (Individual, Team, Professional, Enterprise)

**Components:**
- `Foundation.Presentation` - MVVM framework (Model-View-ViewModel)
- `Foundation.UI.WPF` - WPF controls and themes
- `Foundation.UI.WinForms` - WinForms controls (legacy support)
- `Foundation.UI.Avalonia` - Avalonia UI support (cross-platform)
- `Foundation.Binding` - Advanced data binding
- `Foundation.Commands` - Command pattern infrastructure
- `Foundation.Navigation` - Navigation framework
- `Foundation.Validation` - Validation framework
- `Foundation.Localization` - Internationalization (i18n)
- `Foundation.Theming` - Theme engine (Light, Dark, Custom)

**Key Features:**
- MVVM framework with convention-based binding
- 100+ modern UI controls (data grids, charts, editors)
- Responsive layouts (fluid, adaptive)
- Theme engine (Light, Dark, High Contrast, Custom)
- Material Design and Fluent Design themes
- Data binding with change notification
- Command binding with CanExecute support
- Validation with INotifyDataErrorInfo
- Localization (multi-language support)
- Accessibility (screen reader, keyboard navigation)
- Designer support (Visual Studio, Blend)
- Animations and transitions

**Supported Platforms:**
- WPF (Windows)
- Avalonia UI (Windows, macOS, Linux)
- WinForms (Windows, legacy)

**Deployment Model:**
- NuGet packages
- Visual Studio templates
- Online gallery (themes, templates)

---

### **8. Identity Server**
**Product Name:** Identity Server  
**Tagline:** "Enterprise authentication and authorization server"  
**Target Audience:** Security architects, DevOps engineers, enterprise IT  
**License Type:** Commercial (Server licensing: up to 10/50/unlimited apps)

**Components:**
- `Foundation.IdentityServer` - Core identity server
- `Foundation.IdentityServer.OAuth` - OAuth 2.0 / OpenID Connect
- `Foundation.IdentityServer.SAML` - SAML 2.0 support
- `Foundation.IdentityServer.LDAP` - LDAP/Active Directory integration
- `Foundation.IdentityServer.MFA` - Multi-factor authentication
- `Foundation.IdentityServer.Federation` - Identity federation
- `Foundation.IdentityServer.Admin` - Administration UI
- `Foundation.Security.Client` - Client SDK for applications

**Key Features:**
- OAuth 2.0 / OpenID Connect provider
- SAML 2.0 identity provider (IdP)
- Active Directory / LDAP integration
- Multi-factor authentication (TOTP, SMS, Email)
- Social login (Google, Microsoft, GitHub, etc.)
- Single Sign-On (SSO)
- Identity federation (trust relationships)
- User management (registration, password reset)
- Role-based access control (RBAC)
- Claims-based authorization
- Token management (JWT, reference tokens)
- Session management
- Audit logging (login attempts, token issuance)
- API protection (API scopes, permissions)
- Admin UI (user management, client configuration)

**Deployment Model:**
- On-premises (Windows Server, Linux)
- Docker containers
- Kubernetes Helm charts
- Cloud IaaS (Azure, AWS)
- High availability (clustered deployment)

**System Requirements:**
- Windows Server 2019+, Linux (Ubuntu 20.04+)
- .NET 10 Runtime
- SQL Server 2019+ or PostgreSQL 12+ (user store)
- 4 GB RAM minimum, 8 GB recommended
- 5 GB disk space

**Standards Compliance:**
- OAuth 2.0 (RFC 6749)
- OpenID Connect 1.0
- SAML 2.0
- JWT (RFC 7519)
- FIDO2 / WebAuthn

---

## 📊 **PRODUCT MATRIX**

| **Product** | **Category** | **Target Audience** | **License Model** | **Deployment** |
|-------------|-------------|-------------------|------------------|---------------|
| Data-Access Framework | Data Layer | Backend developers | Per-user/Per-server | NuGet, On-prem, Cloud |
| Communication Framework | Services Layer | Microservices teams | Per-server | NuGet, Docker, K8s |
| Visual Studio Extension | Developer Tools | VS developers | Free/Commercial | VS Marketplace |
| Developer Studio | IDE | Enterprise teams | Per-user | Desktop installer |
| Nexus Server | DevOps/Management | DevOps/Admins | Per-server | On-prem, Docker, K8s |
| Nexus Client | DevOps/Management | Developers/Admins | Free (with server) | Desktop installer |
| UI Framework | Presentation Layer | Desktop developers | Per-user | NuGet, VS templates |
| Identity Server | Security | Security architects | Per-server | On-prem, Docker, K8s |

---

## 🏗️ **PRODUCT DEPENDENCIES**

```
┌─────────────────────────────────────────────────────────────┐
│                     Identity Server                          │
│          (Authentication & Authorization)                    │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ├─────────────────────────┐
                         │                         │
                         ↓                         ↓
┌────────────────────────────────┐    ┌───────────────────────┐
│      Nexus Server              │    │ Communication         │
│ (Management & Monitoring)      │←───│ Framework             │
└────────────────┬───────────────┘    └───────────┬───────────┘
                 │                                 │
                 ↓                                 │
┌────────────────────────────┐                    │
│      Nexus Client          │                    │
│   (Desktop Management)     │                    │
└────────────────────────────┘                    │
                                                   │
                 ┌─────────────────────────────────┤
                 │                                 │
                 ↓                                 ↓
┌────────────────────────────┐    ┌───────────────────────────┐
│   Data-Access Framework    │    │    UI Framework           │
│      (Data Layer)          │    │  (Presentation Layer)     │
└────────────────────────────┘    └───────────────────────────┘
                 │                                 │
                 └────────────┬────────────────────┘
                              │
                              ↓
                 ┌────────────────────────────┐
                 │    Developer Studio        │
                 │    (IDE - Uses All)        │
                 └────────────────────────────┘
                              ↑
                              │
                 ┌────────────────────────────┐
                 │  Visual Studio Extension   │
                 │  (VS Integration)          │
                 └────────────────────────────┘
```

**Dependency Notes:**
- **Identity Server** is foundational - used by Nexus Server, Communication Framework
- **Nexus Server** manages all Foundation Framework applications
- **Nexus Client** connects to Nexus Server for management
- **Developer Studio** integrates all products (development experience)
- **Visual Studio Extension** provides subset of Developer Studio features in VS
- **Data-Access, Communication, UI Frameworks** are independent but can be used together

---

## 📋 **DOCUMENTATION FOLDER STRUCTURE**

```
docs/Products/
├── Data-Access-Framework/
│   ├── Product_Overview.md
│   ├── Technology/
│   │   ├── Architecture.md
│   │   ├── Components/
│   │   │   ├── Foundation.Data.md
│   │   │   ├── Foundation.Caching.md
│   │   │   ├── Foundation.Pooling.md
│   │   │   ├── Foundation.Data.SqlServer.md
│   │   │   ├── Foundation.Data.Oracle.md
│   │   │   └── [Other Providers].md
│   │   └── API_Reference.md
│   ├── Features/
│   ├── Getting_Started/
│   ├── Deployment/
│   ├── Support/
│   ├── Legal/
│   └── Marketing/
│
├── Communication-Framework/
│   ├── Product_Overview.md
│   ├── Technology/
│   │   ├── Architecture.md
│   │   ├── Components/
│   │   │   ├── Foundation.ServiceModel.md
│   │   │   ├── Foundation.Messaging.md
│   │   │   └── Foundation.ServiceDiscovery.md
│   │   └── API_Reference.md
│   ├── Features/
│   ├── Getting_Started/
│   ├── Deployment/
│   ├── Support/
│   ├── Legal/
│   └── Marketing/
│
├── Visual-Studio-Extension/
│   ├── Product_Overview.md
│   ├── Technology/
│   │   ├── Architecture.md
│   │   └── Components/
│   │       ├── Extension_Core.md
│   │       ├── Templates.md
│   │       ├── Code_Generators.md
│   │       └── Analyzers.md
│   ├── Features/
│   ├── Getting_Started/
│   ├── Support/
│   ├── Legal/
│   └── Marketing/
│
├── Developer-Studio/
│   ├── Product_Overview.md
│   ├── Technology/
│   │   ├── Architecture.md
│   │   └── Components/
│   │       ├── Core_IDE.md
│   │       ├── Editor.md
│   │       ├── Designer.md
│   │       └── Debugger.md
│   ├── Features/
│   ├── Getting_Started/
│   ├── Deployment/
│   ├── Support/
│   ├── Legal/
│   └── Marketing/
│
├── Nexus-Server/
│   ├── Product_Overview.md
│   ├── Technology/
│   │   ├── Architecture.md
│   │   └── Components/
│   │       ├── Core_Engine.md
│   │       ├── Monitoring.md
│   │       ├── Logging.md
│   │       └── Deployment.md
│   ├── Features/
│   ├── Getting_Started/
│   ├── Deployment/
│   ├── Support/
│   ├── Legal/
│   └── Marketing/
│
├── Nexus-Client/
│   ├── Product_Overview.md
│   ├── Technology/
│   │   ├── Architecture.md
│   │   └── Components/
│   │       ├── Client_Core.md
│   │       ├── Dashboard.md
│   │       └── Deployment_Tools.md
│   ├── Features/
│   ├── Getting_Started/
│   ├── Support/
│   ├── Legal/
│   └── Marketing/
│
├── UI-Framework/
│   ├── Product_Overview.md
│   ├── Technology/
│   │   ├── Architecture.md
│   │   └── Components/
│   │       ├── Foundation.Presentation.md
│   │       ├── Foundation.UI.WPF.md
│   │       ├── Foundation.Binding.md
│   │       └── Foundation.Theming.md
│   ├── Features/
│   ├── Getting_Started/
│   ├── Deployment/
│   ├── Support/
│   ├── Legal/
│   └── Marketing/
│
└── Identity-Server/
    ├── Product_Overview.md
    ├── Technology/
    │   ├── Architecture.md
    │   └── Components/
    │       ├── Core_Identity.md
    │       ├── OAuth_OpenIDConnect.md
    │       ├── SAML.md
    │       └── MFA.md
    ├── Features/
    ├── Getting_Started/
    ├── Deployment/
    ├── Support/
    ├── Legal/
    └── Marketing/
```

---

## 🎯 **PRODUCT POSITIONING**

### **Development Stack Positioning:**

```
┌─────────────────────────────────────────────────────────┐
│                    Developer Tools                       │
│  ┌──────────────────────┐  ┌─────────────────────────┐ │
│  │ Visual Studio Ext.   │  │  Developer Studio        │ │
│  └──────────────────────┘  └─────────────────────────┘ │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                 Management & Monitoring                  │
│  ┌──────────────────────┐  ┌─────────────────────────┐ │
│  │  Nexus Server        │  │  Nexus Client            │ │
│  └──────────────────────┘  └─────────────────────────┘ │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                        Security                          │
│                  ┌──────────────────┐                   │
│                  │  Identity Server │                    │
│                  └──────────────────┘                    │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                   Application Layers                     │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────┐ │
│  │ UI Framework│  │Communication │  │  Data-Access  │  │
│  │             │  │  Framework   │  │   Framework   │  │
│  └─────────────┘  └──────────────┘  └───────────────┘  │
│  (Presentation)      (Services)          (Data)         │
└─────────────────────────────────────────────────────────┘
```

---

## 💰 **LICENSING MODELS**

### **Framework Products (Data-Access, Communication, UI)**
- **Individual:** Single developer, single workstation - $499/year
- **Team:** Up to 5 developers - $1,999/year
- **Professional:** Up to 25 developers - $7,999/year
- **Enterprise:** Unlimited developers - $19,999/year + support

### **Server Products (Nexus Server, Identity Server)**
- **Starter:** Up to 10 applications - $2,999/year
- **Professional:** Up to 50 applications - $9,999/year
- **Enterprise:** Unlimited applications - $24,999/year + support

### **Developer Tools**
- **Visual Studio Extension:**
  - Community: Free (for individuals, small teams <5)
  - Professional: Included with Framework license
  - Enterprise: Included with Framework license
- **Developer Studio:**
  - Professional: $299/user/year
  - Enterprise: $999/user/year (includes priority support)

### **Client Tools**
- **Nexus Client:** Free (included with Nexus Server subscription)

---

## 📅 **RELEASE ROADMAP**

### **Phase 1: Foundation (Completed)**
- ✅ Data-Access Framework v1.0
- ✅ Communication Framework v1.0 (partial - WCF only)
- ✅ UI Framework v1.0 (partial - WPF only)

### **Phase 2: Developer Tools (Q2 2026)**
- 🔄 Visual Studio Extension v1.0
- 🔄 Developer Studio v1.0 (Beta)

### **Phase 3: Management Platform (Q3 2026)**
- 🔄 Nexus Server v1.0
- 🔄 Nexus Client v1.0

### **Phase 4: Security (Q4 2026)**
- 🔄 Identity Server v1.0

### **Phase 5: Enhancements (2027)**
- 📅 Communication Framework v2.0 (gRPC, Kafka)
- 📅 UI Framework v2.0 (Avalonia cross-platform)
- 📅 Developer Studio v2.0 (macOS, Linux)

---

## 🎁 **PRODUCT BUNDLES**

### **Full Stack Bundle**
**Includes:** Data-Access, Communication, UI Framework  
**Price:** $1,299/user/year (save 20%)

### **Enterprise Suite**
**Includes:** All frameworks + Developer Studio + Nexus Server + Identity Server  
**Price:** $49,999/year (unlimited developers, up to 100 apps)

### **Developer Bundle**
**Includes:** Visual Studio Extension + Developer Studio  
**Price:** $399/user/year (save 15%)

---

**Last Updated:** March 2, 2026  
**Maintained By:** Product Management Team  
**Contact:** products@invitrek.com
