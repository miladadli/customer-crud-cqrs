# Customer Management Service

A robust, production-grade Customer Management API built with **NestJS**, **PostgreSQL**, and **TypeORM**. The system is architected around **Domain-Driven Design (DDD)**, **Clean Architecture**, and **CQRS (Command Query Responsibility Segregation)** principles, backed by strict domain validations, optimized storage, and automated test suites (TDD/BDD).

---

## Architecture & Design Patterns

The project follows the 4-layer **Clean Architecture (Onion)** to decouple enterprise business rules from frameworks, databases, and delivery mechanisms:

```
src/
├── domain/            # Enterprise Core: Entities, Value Objects, Repository Interfaces
│   ├── models/        # Customer Aggregate Root
│   └── value-objects/ # Email, PhoneNumber, BankAccountNumber (self-validating)
├── application/       # Application Logic: Commands, Queries, Orchestration Handlers
│   ├── commands/      # CreateCustomer, UpdateCustomer, DeleteCustomer
│   └── queries/       # GetCustomers, GetCustomerById
├── infrastructure/    # Frameworks & Adapters: TypeORM persistence, Database Mappers
└── presentation/      # Inbound Transport: REST Controllers, DTOs, Swagger documentation
```

### Key Architectural Decisions
- **Domain-Driven Design (DDD):** Domain rules live exclusively inside the core entities and Value Objects (`Email`, `PhoneNumber`, `BankAccountNumber`). Any invalid domain state is rejected immediately before reaching persistence.
- **CQRS Pattern:** Complete segregation of read and write paths:
  - **Write Path (Commands):** Mutations execute via dedicated Command Handlers with domain event dispatching.
  - **Read Path (Queries):** Optimized Query Handlers fetch projections directly for lower latency.
- **Strict Validations:**
  - **Phone Number:** Validated using Google's `libphonenumber-js` to ensure only legitimate mobile formats are accepted.
  - **Compound Uniqueness:** Enforced composite unique constraint on `(FirstName, LastName, DateOfBirth)` to prevent duplicate profile creation.
  - **Email Uniqueness:** System-wide unique constraint on customer email.
- **Storage Optimization:** Phone numbers are normalized and stored as compact values to minimize database disk usage and index footprints.

---

## API Endpoints

Interactive Swagger UI documentation is available at `http://localhost:3000/api`.

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `POST` | `/customers` | Register a new customer (validates mobile, email, and bank account) |
| `GET` | `/customers` | Retrieve a list of all customers |
| `GET` | `/customers/:id` | Fetch detailed information for a specific customer |
| `PUT` | `/customers/:id` | Update an existing customer record |
| `DELETE` | `/customers/:id` | Remove a customer record from the system |

---

## Getting Started

### Prerequisites
- Node.js v20+ and npm
- Docker and Docker Compose

### 1. Environment Configuration
Copy the template configuration file:

```bash
cp .env.example .env
```

Default local database connection values:

```env
DB_HOST=localhost
DB_PORT=5432
DB_USERNAME=postgres
DB_PASSWORD=postgres
DB_NAME=customer_crud
```

### 2. Start Database via Docker
Run the PostgreSQL container:

```bash
docker compose up -d db
```

### 3. Run Application Locally
Install dependencies and launch the development server:

```bash
npm install
npm run start:dev
```

The application will be accessible at `http://localhost:3000`.

---

## Running Full Stack with Docker

To boot both the application and the PostgreSQL database in isolated containers:

```bash
docker compose up --build
```

---

## Testing Suite

The codebase implements comprehensive automated testing following TDD/BDD methodologies:

```bash
# Run unit tests (Value objects, command/query handlers)
npm run test

# Run end-to-end integration tests (HTTP transport, database constraints)
npm run test:e2e

# Run test coverage report
npm run test:cov
```
