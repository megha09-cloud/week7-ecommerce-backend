oo# E-Commerce Backend System with Database Integration

## 📋 Project Overview
This project is an enterprise-grade E-Commerce Backend System designed to handle core online retail operations. Built using **Spring Boot 3.x** and **Spring Data JPA**, the system delivers robust data persistence layer integration against a containerized **PostgreSQL** instance. 

### Core Objectives
* **Relational Database Fundamentals:** Implement standard structural integrity with explicit constraints, custom indexing, and optimized column data types.
* **Automated Schema Management:** Maintain strict database evolutionary track controls using Flyway migrations.
* **Transaction Safety:** Enforce full ACID compliance across order processing and stock allocation routines using managed transactional boundaries.
* **Performance Optimization:** Eliminate standard ORM bottlenecks (such as the N+1 select problem) through customized fetching strategies, connection pools, and query caching mechanisms.

---

## ⚙️ Setup & Installation Instructions

### Prerequisites
* **Java Development Kit (JDK):** Version 17 or higher installed.
* **Apache Maven:** Configured in your system environment path.
* **Docker Desktop:** Installed and running active on your system.

### Step-by-Step Execution Guide

1. **Spin Up the Database Container**
   Launch the pre-configured PostgreSQL 15 database instance using Docker Compose:
   ```bash
   docker compose up -d
   ```
2. Verify Database Health
    Ensure the database service container is up and running on port 5432:

    ```Bash
      docker ps
    ```
3. Compile and Run the Application
Use Maven to clear out old build artifacts, validate packages, and run the boot engine:

    ```Bash
      mvn clean spring-boot:run
    ```
The application will boot up automatically on port 8080.

---

## 🗂️ Project Code Structure
The codebase follows standard enterprise multi-tier architecture, establishing a strict separation of concerns:

```Plaintext
week7-ecommerce-backend/
│── src/main/java/com/ecommerce/
│   ├── EcommerceApplication.java      - Main entry point & component scanner
│   ├── controller/                    - Presentation layer (REST Controllers)
│   │   ├── ProductController.java
│   │   ├── OrderController.java
│   │   ├── UserController.java
│   │   └── PaymentController.java
│   ├── service/                       - Business logic layer & transaction boundaries
│   │   ├── ProductService.java
│   │   ├── OrderService.java
│   │   ├── UserService.java
│   │   └── PaymentService.java
│   ├── repository/                    - Data access layer (Spring Data JPA Repositories)
│   │   ├── ProductRepository.java
│   │   ├── OrderRepository.java
│   │   ├── UserRepository.java
│   │   ├── CategoryRepository.java
│   │   └── PaymentRepository.java
│   ├── model/
│   │   ├── entity/                    - JPA Relational Database Entity Mappings
│   │   │   ├── User.java
│   │   │   ├── Product.java
│   │   │   ├── Category.java
│   │   │   ├── Order.java
│   │   │   ├── OrderItem.java
│   │   │   └── Payment.java
│   │   ├── dto/                       - Decoupled Presentation Data Transfer Objects
│   │   │   ├── ProductDTO.java
│   │   │   ├── OrderDTO.java
│   │   │   ├── UserDTO.java
│   │   │   └── OrderSummaryDTO.java
│   │   └── enums/                     - Safe Domain Application Enums
│   │       ├── OrderStatus.java
│   │       ├── PaymentStatus.java
│   │       └── Role.java
│   ├── config/                        - Application Configurations
│   │   └── CacheConfig.java           - ConcurrentMapCacheManager registration
│   └── exception/                     - Custom business exceptions
│       ├── ResourceNotFoundException.java
│       ├── InsufficientStockException.java
│       └── PaymentFailedException.java
│── src/main/resources/
│   ├── db/migration/                  - Immutable Flyway Schema Migrations
│   │   ├── V1__initial_schema.sql     - Physical DDL Structural Layout
│   │   ├── V2__seed_data.sql          - Localized Indian Dataset Seeding DML
│   │   └── V3__add_indexes.sql        - Index Optimization DDL
│   └── application.yml                - Datasource & Hibernate Runtime Parameters
│── docker-compose.yml                 - Container configuration for PostgreSQL
│── .gitignore                         - Specifies intentionally untracked files to ignore
└── pom.xml                            - Maven Dependency Project Object Model

```

---

## 🛠️ Technical Details & Architectural Design
### Database Schema Matrix
The database structural design contains 6 primary physical entity tables organized with explicit foreign key constraints:

* users: Stores profile keys (id, name, email, password, role).

* categories: Self-referencing hierarchical structure (id, name, parent_category_id).

* products: Catalog tracking with relational indexing (id, name, price, stock, category_id).

* orders: Base checkout metrics linked to users (id, order_number, user_id, total_amount, status).

* order_items: Bidirectional join table capturing specific items per order lifecycle (id, order_id, product_id, quantity).

* payments: One-to-One ledger mapping order payment histories (id, order_id, amount, status, transaction_id).

### Key Implementations & Optimizations
* Transaction Boundaries: Order checkouts leverage @Transactional(rollbackFor = Exception.class). If an order item demands more stock than available, an InsufficientStockException triggers an automatic runtime database rollback, preventing data inconsistencies.

* Query Optimization: Eliminates N+1 select bottlenecks using customized LEFT JOIN FETCH JPQL structures inside repositories to load lazy relationships efficiently in a single database round-trip.

* Index Tuning: Explicit indices (idx_product_name, idx_product_category, idx_orders_user) are added to high-traffic columns to achieve sub-50ms query execution turnarounds.

* Caching Strategy: Active catalogs are bound via @Cacheable("products") to intercept database calls for static, high-volume search routines.

### 🔗 Exposed REST API Mappings

| Method | Endpoint | Description | Access |
| :--- | :--- | :--- | :--- |
| **GET** | `/api/products` | Fetch paginated product catalog results | Public |
| **GET** | `/api/users/profile?userId={id}` | Retrieve clean profile data objects | Public |
| **POST** | `/api/orders?userId={id}` | Process transaction-safe checkouts | Public |
| **GET** | `/api/payments/status/{txnId}` | View historical payment transaction statuses | Public |

---

## 🧪 Testing Evidence & Database Migrations
On startup, Flyway validates past schema steps against internal metadata locks. Data migrations are systematically handled across 3 strict sequential scripts:

1. V1__initial_schema.sql: Establishes basic relational tables and system constraints.

2. V2__seed_data.sql: Seeds records using realistic Indian user profiles (e.g., Aarav Sharma, Ananya Iyer) and electronics products (Boat Rockerz, OnePlus Nord Buds, Portronics).

3. V3__add_indexes.sql: Injects custom indices to ensure swift filtering across production data.

---

## 📸 Visual Documentation & Verification Evidence

Below are the operational screenshots verifying successful container deployment, automated schema creation, and active API response patterns.

### 1. PostgreSQL Container Infrastructure
Verified active and listening on port `5432` inside Docker Desktop.
![Docker Container Status] <img width="1918" height="1198" alt="image" src="https://github.com/user-attachments/assets/898b94ad-b3e5-4f90-82ef-f90dbffec5f9" />



### 2. Spring Boot Engine Boot Mappings & Flyway Logs
Terminal logs confirming HikariCP connection pool acquisition and successful deployment of migrations V1, V2, and V3.
![VS Code Execution Logs] <img width="1918" height="1198" alt="image" src="https://github.com/user-attachments/assets/0e60667e-8a1a-4c96-9836-dedeb480e069" />


### 3. User Data Presentation Layer
Verified lookup validation showcasing seeded dataset profile structures.
![User Profile API Response] <img width="1180" height="655" alt="image" src="https://github.com/user-attachments/assets/449f484f-744b-49c7-97a3-788730276776" />


### 4. Paginated Product Catalog API Surface
Verified JPA Pageable metadata tracking and active localized inventory records.
![Product Catalog API Response] <img width="1918" height="681" alt="image" src="https://github.com/user-attachments/assets/dfee48b2-eb0c-4d13-b047-59715f4746b5" />

## 🎓 About the Developer

Name: Megha Gupta 








