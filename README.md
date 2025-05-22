# Template-Dotnet

Simple implementation in .NET 9 of the clean architecture with DDD without using mediatR

---

## 🚀 Overview

**Project Template-Dotnet** is a .NET 9 boilerplate project designed to serve as a robust and modular foundation for future application development. It demonstrates best practices in **Clean Architecture**, **Domain-Driven Design (DDD)**, and **Command Query Responsibility Segregation (CQRS)** without relying on the MediatR NuGet package.

The initial implementation showcases these principles through a simple **product purchasing application**.

### ✨ Guiding Principles

This template is built upon the following core principles:

* **Clean Architecture:** Enforces separation of concerns, dependency inversion, and high testability.
* **Domain-Driven Design (DDD):** Models core business logic using entities, aggregates, value objects, repositories, domain services, and domain events.
* **Command Query Responsibility Segregation (CQRS):** Separates read and write operations for optimized data access and scalability, implemented without MediatR.
* **Modularity:** Key technical and feature implementations (e.g., database, ORM, API style, notifications, authentication, analytics) are designed as interchangeable modules.
* **Fake Data:** Primarily uses fake data generators for demonstration and testing purposes.
* **.NET 9:** Leverages the latest features and capabilities of the .NET 9 framework.
* **Testability:** Emphasizes comprehensive automated testing (unit, integration).

### 📦 Core Functionality (Modules)

The template includes the following core modules, initially demonstrated through a product purchasing application:

* 🛍️ **Product Catalog Module:** Allows users to view a list of products.
* 🛒 **Order Management Module:** Enables users to add products to an order, view and modify the order, add shipping and payment details, validate the order, and view order status. Manages the order lifecycle through various statuses (`Draft`, `Pending_Payment`, `Payment_Failed`, `In_Progress`, `Shipped`, `Delivered`, `Cancelled`).
* 💳 **Finance Service Module (Simulated):** Handles simulated payment processing and invoice generation, triggered by domain events.
* 🚚 **Shipping Service Module (Simulated):** Manages the simulated shipping process, triggered by domain events.
* 🔔 **Realtime Notification Module (Modular):** Provides users with realtime updates on order status changes (e.g., via SignalR, WebSockets). This module is designed to be optional.

### 🏗️ Architectural Highlights

* **Clean Architecture:** The solution structure adheres to the principles of Clean Architecture.
    * `Domain`: Contains enterprise-wide logic and types. Entities, Value Objects, Domain Events, Aggregates, Repositories interfaces.
    * `Application`: Contains application-specific logic. Application Services, Commands, Queries, DTOs. Orchestrates the domain.
    * `Infrastructure`: Contains external concerns like database access, file system access, network communication, etc. Implements interfaces defined in Application or Domain layers.
    * `Presentation` / `API`: The entry point of the application (e.g., ASP.NET Core API, MAUI App).
* **Domain-Driven Design (DDD):** Core business logic is modeled using DDD patterns.
* **CQRS (No MediatR):** Command, Query, and Event handling mechanisms are implemented directly, providing a clear separation of write and read paths without external dependencies like MediatR.

### 🛠️ Technology Stack

* **Framework:** .NET 9
* **Frontend:** .NET MAUI (for mobile clients)
* **API Layer (Configurable):**
    * ASP.NET Core MVC Controllers
    * ASP.NET Core Minimal APIs
* **Data Persistence (Configurable):**
    * **Databases:** SQLite, PostgreSQL
    * **ORMs:** Entity Framework Core, Dapper
* **Testing:** Emphasis on NUnit for unit and integration tests.

### ⚙️ Cross-Cutting Concerns

The template addresses several cross-cutting concerns with a focus on modularity:

* 🔐 **Authentication & Authorization (Modular):**
    * No Authentication Mode (standalone, local data).
    * Authenticated Mode:
        * Third-Party: Google (OAuth 2.0, OpenID Connect).
        * Local: Username/password with secure hashing (e.g., Argon2, scrypt, PBKDF2) and credentials in a separate DB.
        * Account Linking.
    * Configurable at project setup.
* 🌍 **Localization & Internationalization (L10n & I18n):**
    * UI text localization (e.g., English, French).
    * Locale-based data formatting (dates, numbers, currency).
* 🧪 **Automated Testing:**
    * Unit tests for core logic.
    * Integration tests for component interactions.
* 📊 **Analytics (Modular for Configuration 2):**
    * Basic usage analytics (feature usage, session duration).
    * Designed to be easily enabled/disabled.

### 🔧 Supported Configurations

The template is designed to be scaffolded into different configurations:

**1. Configuration 1: Standalone In-App (No Authentication)**
    * **Frontend:** MAUI mobile app.
    * **Backend Logic:** In-process within MAUI.
    * **API Layer:** N/A.
    * **Authentication:** None.
    * **Database:** SQLite.
    * **ORM:** EFCore.
    * **Realtime Notifications:** Optional (local in-app).
    * **Analytics:** Deactivated.

**2. Configuration 2: Multi-User with API Backend (Authentication Enabled)**
    * **Frontend:** MAUI mobile app.
    * **Backend Logic:** Hosted ASP.NET Core application (API).
    * **Authentication:** Google OAuth & Local (separate credential DB), Account Linking.
    * **Database (App Data):** PostgreSQL.
    * **Database (Auth Credentials):** Separate PostgreSQL.
    * **Realtime Notifications:** Optional (server-to-client push).
    * **Analytics:** Optional.
    * **Sub-Configurations (Data Access & API Style):**
        * **2.A:** ORM: EFCore, API: Controllers.
        * **2.B:** ORM: EFCore, API: Minimal APIs.
        * **2.C:** ORM: Dapper, API: Controllers.
        * **2.D:** ORM: Dapper, API: Minimal APIs.

---

## 🚀 Getting Started

*(This section would typically include instructions on how to set up the project, prerequisites, build steps, and how to run the different configurations.)*

**Prerequisites:**
* .NET 9 SDK
* (Other tools depending on configuration, e.g., Docker for PostgreSQL)

**Build & Run:**
```bash
# Clone the repository
git clone https://your-repository-url/Project-Template-Dotnet.git
cd Project-Template-Dotnet

# Restore dependencies (example for a solution file)
dotnet restore

# Build the solution
dotnet build

# Run (specific steps will vary based on chosen configuration and startup project)
# e.g., dotnet run --project src/Presentation/API/ProjectName.API.csproj
# e.g., for MAUI, open solution in IDE and run on target device/emulator
```
*(Detailed instructions for selecting and running specific configurations (e.g., using build scripts, conditional compilation, or different solution files) will go here.)*

---

## ✅ Running Tests

*(This section would describe how to execute the automated tests.)*

```bash
# Navigate to the test project directory or run from solution level
dotnet test
```
Ensure you have the necessary test runners configured (e.g., NUnit).

---

## 💡 Future Possibilities

* **FP-001: Admin Panel:** An administrative interface for managing products, users, etc. The current architecture is designed to accommodate such future additions.

---

## 📋 Non-Functional Requirements Highlights

* 🛡️ **Security:** Adherence to industry-standard protocols (OAuth 2.0, OpenID Connect), secure password hashing, data protection, and consideration for OWASP Top 10.
* 🔧 **Maintainability & Extensibility:** Emphasis on clean code, consistent standards, and a modular design for ease of updates and extensions.
* 📈 **Scalability (for Configuration 2):** Design for stateless services and leveraging PostgreSQL's scaling capabilities.
* ⚡ **Performance:** Optimized data querying, asynchronous operations, and minimized resource consumption for a responsive UI.

---

## 📜 License

This project is licensed under the [MIT License](LICENSE.md).