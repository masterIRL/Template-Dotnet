# Functional Specification Document: Project Template-Dotnet

**Version:** 1.0
**Date:** May 22, 2025

**1. Introduction**
This document outlines the functional specifications for "Project Template-Dotnet," a .NET 9 boilerplate project. The primary goal is to establish a robust, modular template demonstrating best practices in Clean Architecture, Domain-Driven Design (DDD), and Command Query Responsibility Segregation (CQRS) without relying on the MediatR NuGet package. This template will serve as a foundation for future projects, initially implementing a simple product purchasing application.

---

**2. Guiding Principles**
* **Clean Architecture:** The solution will adhere to the principles of Clean Architecture, ensuring separation of concerns, dependency inversion, and testability.
* **Domain-Driven Design (DDD):** Core business logic will be modeled using DDD concepts, including entities, aggregates, value objects, repositories, and domain services. Domain events will be a key part of inter-module communication.
* **Command Query Responsibility Segregation (CQRS):** The project will implement CQRS to separate read and write operations, allowing for optimized data access and scalability.
* **No MediatR:** The project will explicitly avoid the use of the MediatR library, implementing its own mechanisms for command, query, and event handling.
* **Modularity:** Key features and technical implementations (e.g., database, ORM, API style, notifications, authentication, analytics) will be designed as interchangeable modules to allow for flexible project scaffolding.
* **Fake Data:** For demonstration and testing purposes, the application will primarily use
    fake data generators.
* **.NET 9:** The project will be built using the latest features and capabilities of .NET 9.
* **Testability:** Comprehensive automated tests (unit, integration, and potentially end-to-end for core paths) are integral to the project.

---

**3. Core Functionality (Modules)**

**3.1. Product Catalog Module**
    * **UC-PC-001: View Product List:** Users shall be able to view a list of available products.
        * Each product entry shall display at least a name, description, price, and an image (placeholder).
        * Data will be sourced from a predefined fake data set.

**3.2. Order Management Module**
    * **UC-OM-001: Add Product to Order:** Users shall be able to select a product from the product list and add it to their current order.
        * The system shall allow specifying the quantity of the product to be added.
    * **UC-OM-002: View Current Order:** Users shall be able to view the current state of their order, including:
        * List of selected products and their quantities.
        * Price for each item and the subtotal.
        * Total order amount.
    * **UC-OM-003: Modify Order Item Quantity:** Users shall be able to change the quantity of an item already in their order.
        * If quantity becomes zero, the item should be considered for removal (see UC-OM-004).
    * **UC-OM-004: Remove Product from Order:** Users shall be able to remove a product entirely from their current order.
    * **UC-OM-005: Add Shipping Address:** Users shall be able to add/select a shipping address for the order.
        * Address fields: Street, City, State/Province, Postal Code, Country.
    * **UC-OM-006: Add Payment Information:** Users shall be able to add/select payment details (simulated).
        * For simplicity, this can be a mock payment selection (e.g., "FakeCard ending in 1234").
    * **UC-OM-007: Validate Order:** Users shall be able to validate their order once all necessary information (products, address, payment details) is provided.
        * Upon validation, the order status shall change to `Pending_Payment` (or directly to `In_Progress` if payment simulation is immediate post-validation).
        * A domain event (`OrderValidatedEvent`) shall be raised.
    * **UC-OM-008: View Order Status:** Users shall be able to view the current status of their past and current orders.
        * Possible statuses: `Draft`, `Pending_Payment`, `Payment_Failed`, `In_Progress`, `Shipped`, `Delivered`, `Cancelled`.
    * **UC-OM-009: Order Lifecycle Management:** The system shall manage order status transitions based on domain events from other services (Finance, Shipping).
        * `Draft`: Initial state of an order being built.
        * `Pending_Payment`: Order validated, awaiting payment confirmation.
        * `Payment_Failed`: Payment processing failed.
        * `In_Progress`: Payment confirmed, order processing initiated.
        * `Shipped`: Order has been dispatched by the shipping service.
        * `Delivered`: Order delivery confirmed (simulated).
        * `Cancelled`: Order has been cancelled (mechanism for cancellation TBD, potentially an admin function or user option before `In_Progress`).

**3.3. Finance Service Module (Simulated)**
    * **UC-FS-001: Process Payment & Generate Invoice (Handler for `OrderValidatedEvent` or a specific `PaymentProcessingRequestedEvent`):**
        * This service will be triggered when an order is validated and ready for payment.
        * It will simulate invoice generation (no actual PDF/document generation required, just a conceptual step).
        * It will simulate a payment check. This simulation can have a configurable success/failure rate.
        * If payment is successful:
            * An `OrderPaymentConfirmedEvent` domain event shall be raised.
            * The order status (managed by Order Management Module via event handling) shall be updated to `In_Progress`.
        * If payment fails:
            * An `OrderPaymentFailedEvent` domain event shall be raised.
            * The order status shall be updated to `Payment_Failed`.

**3.4. Shipping Service Module (Simulated)**
    * **UC-SS-001: Process Shipment (Handler for `OrderPaymentConfirmedEvent`):**
        * This service will be triggered when an order's payment is confirmed.
        * It will simulate the shipping process (e.g., package pickup, transit, delivery). This can involve configurable delays.
        * During the simulated process, it can raise domain events like:
            * `OrderShippedEvent`: When the package is notionally "shipped." The order status will be updated to `Shipped`.
            * `OrderDeliveredEvent`: When the package is notionally "delivered." The order status will be updated to `Delivered`.

**3.5. Realtime Notification Module (Modular)**
    * **UC-RN-001: Notify User of Order Status Change:** Users shall receive realtime notifications regarding changes in their order status (e.g., "Payment Confirmed," "Order Shipped").
        * This module must be designed so it can be easily enabled or disabled during project setup/build.
        * Implementation details (e.g., SignalR, WebSockets, push notifications for MAUI) are secondary to the functional requirement of providing status updates in realtime if enabled.
        * This module will subscribe to relevant domain events (e.g., `OrderPaymentConfirmedEvent`, `OrderShippedEvent`, `OrderDeliveredEvent`, `OrderPaymentFailedEvent`).

---

**4. Cross-Cutting Concerns**

**4.1. Authentication and Authorization**
    * **CC-AA-001: No Authentication Mode:** The application must support a mode where no user authentication is required. The app functions for a single, anonymous user. Data is typically local to the device/session.
    * **CC-AA-002: Authenticated Mode:** The application must support a mode where users can authenticate.
        * **CC-AA-002.1: Third-Party Authentication (Google):** Users shall be able to register and log in using their Google accounts, following industry-standard protocols (e.g., OAuth 2.0, OpenID Connect).
        * **CC-AA-002.2: Local Authentication:** Users shall be able to create a local account (username/email and password) and log in.
            * Password hashing and storage must follow current security best practices (e.g., Argon2, scrypt, or PBKDF2).
            * Features like password reset and email verification should be considered (though implementation depth can be basic for a template).
        * **CC-AA-002.3: Account Linking:** If a user authenticates via a third-party provider (Google) and also creates/uses local authentication with the same email, these should be linked to a single underlying user account/profile in the application's primary database.
        * **CC-AA-002.4: Separate Local Auth Database:** User credentials for local authentication (usernames, hashed passwords, salts, related security info) must be managed in a separate security-focused database. Other user profile data can reside in the main application database.
    * **CC-AA-003: Modularity:** The choice of authentication mode (none vs. authenticated) and enabled providers (Google, Local) should be configurable at project setup/build time.

**4.2. Localization & Internationalization (L10n & I18n)**
    * **CC-LI-001: UI Text Localization:** All user-facing text in the UI (labels, messages, button text, etc.) shall be externalized and support localization.
        * At least two languages should be supported as an example (e.g., English and French).
    * **CC-LI-002: Data Formatting:** Dates, times, numbers, and currency shall be formatted according to the user's locale or a selected application locale.

**4.3. Automated Testing**
    * **CC-AT-001: Unit Tests:** Core business logic within domain entities, services, command handlers, and query handlers shall be covered by unit tests.
    * **CC-AT-002: Integration Tests:** Interactions between components (e.g., application services and repositories, API endpoints to application services) shall be covered by integration tests.
        * Tests should cover different data persistence configurations where feasible.
    * **CC-AT-003: Test Coverage:** Aim for a high level of test coverage to ensure regressions are caught when new features are implemented or existing ones are refactored.

**4.4. Data Persistence (Modular)**
    * **CC-DP-001: Database Agnostic Design:** The core application and domain layers shall be persistence ignorant.
    * **CC-DP-002: SQLite Support:** The application must be configurable to use SQLite as its backing database. This is particularly relevant for the "no authentication" individual user configuration.
    * **CC-DP-003: PostgreSQL Support:** The application must be configurable to use PostgreSQL as its backing database. This is relevant for configurations supporting multiple authenticated users.
    * **CC-DP-004: EF Core Support:** The application must provide a data access implementation using Entity Framework Core.
    * **CC-DP-005: Dapper Support:** The application must provide a data access implementation using Dapper.
    * **CC-DP-006: Modularity:** The choice of database (SQLite/Postgres) and ORM (EFCore/Dapper) must be configurable at project setup/build time. Each specific combination required by the configurations must be achievable.

**4.5. API Layer (Modular - for Authenticated Configurations)**
    * **CC-AL-001: Controller-Based API Support:** The application must be configurable to expose its functionalities via traditional ASP.NET Core MVC Controllers.
    * **CC-AL-002: Minimal API Support:** The application must be configurable to expose its functionalities via ASP.NET Core Minimal APIs.
    * **CC-AL-003: Request Validation:** All API endpoints must validate incoming requests (data format, required fields, basic business rules where appropriate at the edge).
    * **CC-AL-004: API Versioning:** APIs must support versioning (e.g., URL-based, header-based).
    * **CC-AL-005: Modularity:** The choice of API style (Controllers/Minimal APIs) must be configurable at project setup/build time.

**4.6. Frontend**
    * **CC-FE-001: MAUI Mobile Client:** The primary user interface will be a .NET MAUI application for mobile platforms (iOS and Android).
        * The MAUI app will interact directly with application services (for Configuration 1) or with the API layer (for Configuration 2).

**4.7. Performance**
    * **CC-PE-001: Optimized Performance:** The application must be designed and implemented following industry best practices for performance.
        * This includes efficient data querying, asynchronous operations where appropriate, and minimizing resource consumption.
    * **CC-PE-002: Responsiveness:** The UI should remain responsive during background operations.

**4.8. Analytics (Modular for Configuration 2)**
    * **CC-AN-001: Usage Analytics:** For configurations involving authenticated users and an API backend (Configuration 2), basic usage analytics should be implemented.
        * Examples: Track feature usage frequency, user session duration.
        * This module must be designed so it can be easily enabled or disabled during project setup/build.
        * Specific analytics platform integration details are TBD, focus on the hook points for capturing events.

---

**5. Configurations**

**5.1. Configuration 1: Standalone In-App (No Authentication)**
    * **Frontend:** MAUI mobile app.
    * **Backend Logic:** In-process within the MAUI application (direct calls to application services).
    * **API Layer:** Not applicable (no external API).
    * **Authentication:** None.
    * **Database:** SQLite.
    * **ORM:** EFCore.
    * **Realtime Notifications:** Optional (can be enabled/disabled, likely local in-app events if enabled).
    * **Analytics:** Deactivated.

**5.2. Configuration 2: Multi-User with API Backend (Authentication Enabled)**
    * **Frontend:** MAUI mobile app.
    * **Backend Logic:** Hosted ASP.NET Core application exposing an API.
    * **Authentication:**
        * Google OAuth 2.0 / OpenID Connect.
        * Local username/password authentication (credentials in a separate database).
        * Account linking.
    * **Database (Application Data):** PostgreSQL.
    * **Database (Local Auth Credentials):** Separate PostgreSQL database (or schema, to be architecturally decided for maximum security and separation).
    * **Realtime Notifications:** Optional (can be enabled/disabled, server-to-client push).
    * **Analytics:** Optional (can be enabled/disabled).
    * **Sub-Configurations (Data Access & API Style - all must be achievable by selecting modules):**
        * **Config 2.A:** ORM: EFCore, API: Controllers.
        * **Config 2.B:** ORM: EFCore, API: Minimal APIs.
        * **Config 2.C:** ORM: Dapper, API: Controllers.
        * **Config 2.D:** ORM: Dapper, API: Minimal APIs.

---

**6. Future Possibilities**
    * **FP-001: Admin Panel:** An administrative interface for managing products, users, and potentially viewing system health or order statistics. This is not part of the initial scope but the architecture should not preclude its future addition.

---

**7. Non-Functional Requirements**

**7.1. Security**
    * **NFR-SEC-001: Industry-Standard Protocols:** Authentication and authorization mechanisms must use industry-standard protocols and best practices (e.g., OAuth 2.0, OpenID Connect, secure password hashing).
    * **NFR-SEC-002: Data Protection:** Sensitive data (e.g., PII, if any were to be collected beyond the scope of this template's order example) must be handled securely.
    * **NFR-SEC-003: OWASP Top 10:** Considerations for common web application vulnerabilities (OWASP Top 10) should be made, especially for the API layer.

**7.2. Maintainability & Extensibility**
    * **NFR-MAIN-001: Clean Code:** Code should be well-documented, follow consistent coding standards, and be easily understandable.
    * **NFR-MAIN-002: Modular Design:** The modular design (as specified throughout) is key to maintainability and extensibility, allowing parts of the system to be updated or replaced with minimal impact on other parts.

**7.3. Scalability (for Configuration 2)**
    * **NFR-SCAL-001: Stateless Services:** Application services exposed via the API should be designed to be stateless where possible to facilitate horizontal scaling.
    * **NFR-SCAL-002: Database Scalability:** PostgreSQL selection allows for various scaling strategies if needed in a real-world deployment.

---