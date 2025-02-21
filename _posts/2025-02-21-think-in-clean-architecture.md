---
title: "Think in Clean Architecture"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - architecture
  - clean
  - ddd
  - mvc
---

## Introduction

**Clean Architecture** is a software design philosophy that prioritizes separation of concerns, testability, and maintainability by decoupling business logic from frameworks, databases, and UIs. Introduced by Robert C. Martin (Uncle Bob), it ensures the core application remains independent of external tools. This article explores Clean Architecture, its synergy with **Domain-Driven Design (DDD)**, and how it contrasts with or enhances traditional **MVC (Model-View-Controller)**. We’ll also dissect how to organize packages for these architectures.

## Key Terminologies

| Term                  | Explanation                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| **Entities**          | Core business objects encapsulating domain rules (e.g., `User`, `Order`).   |
| **Use Cases**         | Application-specific workflows that orchestrate entities (e.g., `CreateOrder`).|
| **Interface Adapters**| Convert data between external systems (e.g., APIs, databases) and use cases.  |
| **Frameworks/Drivers**| External tools (e.g., Spring, React) used in the outermost layer.             |
| **Dependency Rule**   | Dependencies point inward: inner layers never depend on outer layers.         |

## Clean Architecture Layers

Clean Architecture organizes code into four concentric layers:

1. **Entities**: Pure business objects with no external dependencies.
2. **Use Cases**: Application logic that directs entities and interface adapters.
3. **Interface Adapters**: Gateways to external systems (e.g., REST controllers, ORM mappings).
4. **Frameworks/Drivers**: Infrastructure details (e.g., databases, UI frameworks).

<div class="mermaid">
graph TD
  A[Entities] --> B[Use Cases]
  B --> C[Interface Adapters]
  C --> D[Frameworks/Drivers]
  classDef layer fill:#f9f,stroke:#333,stroke-width:2px;
  class A,B,C,D layer;
</div>

### Dependency Rule in Action

The **Dependency Rule** ensures outer layers depend on inner layers, never vice versa. For example, a database (outer layer) depends on an interface adapter, which implements a repository interface defined in the use case layer.

<div class="mermaid">
graph TD
  D[Database] --> C[RepositoryImpl]
  C --> B[RepositoryInterface]
  B --> A[Use Case]
  classDef outer fill:#ff9,stroke:#333;
  classDef inner fill:#9f9,stroke:#333;
  class D outer
  class C,B inner
  class A outer
</div>

---

## Domain-Driven Design (DDD) and Clean Architecture

DDD complements Clean Architecture by providing tactical patterns for modeling complex domains. Its layers align with Clean Architecture as follows:

| DDD Layer              | Clean Architecture Equivalent      | Responsibility                               |
|------------------------|--------------------------------------|--------------------------------------------- |
| **Domain Layer**       | Entities                             | Define domain models, aggregates, and rules. |
| **Application Layer**  | Use Cases                            | Coordinate domain objects and workflows.     |
| **Infrastructure Layer**| Interface Adapters + Frameworks     | Implement persistence, APIs, and UI.         |

### Package Structure for DDD + Clean Architecture

```
com.example.app  
├── domain           # Clean's Entities  
│   ├── model  
│   │   └── User.java       # Aggregate root  
│   └── service     # Domain services  
├── application    # Clean's Use Cases  
│   ├── usecases  
│   │   └── CreateUser.java  
│   └── dto        # Data Transfer Objects  
└── infrastructure   # Interface Adapters + Frameworks  
    ├── persistence  
    │   └── UserRepositoryImpl.java  
    └── web  
        └── UserController.java  
```

---

## Custom MVC Architecture in Clean Style

Traditional MVC separates **Model** (data), **View** (UI), and **Controller** (logic). In Clean Architecture, MVC can be adapted to align with layers:

| MVC Component  | Clean Architecture Equivalent      |
|----------------|--------------------------------------|
| **Model**      | Entities + Use Cases                 |
| **View**       | Interface Adapters (UI)              |
| **Controller** | Interface Adapters (API)             |

### MVC Package Structure with Clean Layers

```
com.example.app  
├── core         # Entities + Use Cases  
│   ├── model  
│   │   └── Product.java  
│   └── service  
│       └── OrderService.java  
├── adapters     # Interface Adapters  
│   ├── web  
│   │   ├── ProductController.java   # Controller  
│   │   └── ProductView.java         # View (UI)  
│   └── persistence  
│       └── ProductRepositoryImpl.java  
└── main         # Frameworks/Drivers  
    └── Application.java           # Bootstraps the app  
```

---

## Comparing Package Structures

| Architecture | Package Organization Strategy                                     |
|--------------|-------------------------------------------------------------------|
| **Clean**    | By layer: `entities`, `usecases`, `adapters`, `frameworks`.         |
| **DDD**      | By bounded context and layer: `domain`, `application`, `infrastructure`. |
| **MVC**      | By component: `model`, `view`, `controller`.                        |

---

## When to Use Which Architecture?

- **Clean Architecture**: Ideal for long-term, complex systems requiring strict separation (e.g., enterprise apps).
- **DDD**: Best for domains with intricate business rules (e.g., banking, supply chain).
- **MVC**: Suitable for simpler apps where rapid UI development is prioritized (e.g., basic CRUD apps).

---

## Conclusion

Clean Architecture provides a robust blueprint for maintainable systems by isolating business logic from external concerns. When combined with DDD, it excels in modeling complex domains, while adapting MVC keeps UI-focused apps structured. By organizing packages according to architectural boundaries, teams can achieve clarity, scalability, and testability. Whether building a microservice with DDD or a web app with MVC, Clean Architecture’s principles ensure your codebase remains resilient to change.

<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true });
</script>

**Key Features of the Article:**
- **Layered Visualizations:** Mermaid diagrams illustrate layer dependencies and component relationships.
- **Package Structure Examples:** Clear directory trees for Clean, DDD, and MVC.
- **Comparative Tables:** Contrasts architectures and their organizational strategies.
- **Practical Guidance:** Explains when to use each architecture based on project needs.
```
