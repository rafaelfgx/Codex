# Architecture

## Clean Architecture

Proposed by Robert C. Martin (Uncle Bob), organizes software into concentric layers where dependencies point inward, ensuring that business rules remain independent of frameworks, databases, UI, and other external technologies.

### Basic Structure

- **Entities**: Enterprise business rules.
- **Use Cases**: Application-specific business rules.
- **Interface Adapters**: Controllers, presenters, gateways, and other adapters.
- **Frameworks & Drivers**: Database, UI, web framework, external systems, etc.

### Example

```
customer
├── domain
│   └── entities
│       └── Customer.java
│
├── application
│   ├── usecases
│   │   ├── CreateCustomerUseCase.java
│   │   └── FindCustomerUseCase.java
│   └── ports
│       └── CustomerRepository.java
│
├── adapters
│   ├── controllers
│   │   └── CustomerController.java
│   ├── dtos
│   │   ├── CreateCustomerRequest.java
│   │   └── CustomerResponse.java
│   └── mappers
│       └── CustomerMapper.java
│
└── infrastructure
    └── mongo
        ├── documents
        │   └── CustomerDocument.java
        ├── repositories
        │   └── CustomerMongoRepository.java
        └── MongoConfiguration.java
```

---

## Hexagonal Architecture (Ports and Adapters)

Proposed by Alistair Cockburn, focuses on isolating the application core from external systems through ports and adapters that communicate through those ports.

### Basic Structure

- **Domain**: Business model and domain logic.
- **Ports:** Interfaces defining communication contracts. Inbound (driving) ports expose the application core, while outbound (driven) ports define its dependencies on external systems.
- **Adapters:** Components that connect external technologies to the application through ports. Inbound adapters invoke the application, while outbound adapters integrate with external systems.

### Example

```
customer
├── domain
│   └── entities
│       └── Customer.java
│
├── application
│   ├── usecases
│   │   ├── CreateCustomerUseCase.java
│   │   └── FindCustomerUseCase.java
│   └── ports
│       └── outbound
│           └── CustomerRepository.java
│
├── adapters
│   ├── inbound
│   │   ├── controllers
│   │   │   └── CustomerController.java
│   │   ├── dtos
│   │   │   ├── CreateCustomerRequest.java
│   │   │   └── CustomerResponse.java
│   │   └── mappers
│   │       └── CustomerMapper.java
│   │
│   └── outbound
│       └── mongo
│           ├── documents
│           │   └── CustomerDocument.java
│           └── repositories
│               └── CustomerMongoRepository.java
│
└── infrastructure
    └── mongo
        └── MongoConfiguration.java
```

---

## Key Differences

**Clean Architecture:** Organizes software into concentric layers and enforces the Dependency Rule, ensuring that source code dependencies always point toward the business rules.

**Hexagonal Architecture:** Organizes software around ports and adapters, isolating the application core from external technologies through well-defined communication contracts.