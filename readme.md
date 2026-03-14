# 🏗️ System Design

## 🏛️ What is System Design?

System Design = How to architect a large-scale
application that handles millions of users.

### Two Types

| Type | What It Is | Asked At |
|---|---|---|
| **HLD** — High Level Design | Overall architecture, components, how they connect | All companies |
| **LLD** — Low Level Design | Class diagrams, OOP design, code structure | Product companies |

> 💡 As a fresher → Focus on **LLD first** then HLD basics

---

## 🏗️ Core Concepts

### 1️⃣ Client-Server Architecture
```
[Browser / Mobile App]
          ↓  HTTP Request
   [Load Balancer]
          ↓
[Server 1] [Server 2] [Server 3]
          ↓
      [Database]
```

### 2️⃣ Monolithic vs Microservices
```
MONOLITH                        MICROSERVICES
━━━━━━━━━━━━━━━━━━━━━━━━        ━━━━━━━━━━━━━━━━━━━━━━━━
┌──────────────────────┐        ┌──────┐ ┌──────┐ ┌──────┐
│      FinVault        │        │ Auth │ │Accts │ │Trans │
│  Auth + Accounts +   │        └──────┘ └──────┘ └──────┘
│  Transfers + Audit   │           │        │        │
│    One Database      │        [DB1]    [DB2]    [DB3]
└──────────────────────┘
✅ Simple to build               ✅ Scale independently
✅ Easy to debug                 ✅ Teams own services
❌ Hard to scale one part        ❌ Complex to manage
```

### 3️⃣ Load Balancer
```
            [Load Balancer]
           /       |       \
    [Server 1] [Server 2] [Server 3]

Purpose → Distribute traffic evenly
Tool    → AWS Elastic Load Balancer
```

### 4️⃣ Caching
```
WITHOUT CACHE                   WITH CACHE
━━━━━━━━━━━━━━━━━━━━━━━━        ━━━━━━━━━━━━━━━━━━━━━━━━
User → Server → DB              User → Cache ✅ fast!
(hits DB every request)              ↓ miss
                                Server → DB → Cache → User

Tools         → Redis, Memcached
Spring Boot   → @Cacheable annotation
FinVault use  → Cache account balance
```

### 5️⃣ Database Scaling
```
VERTICAL SCALING                HORIZONTAL SCALING
━━━━━━━━━━━━━━━━━━━━━━━━        ━━━━━━━━━━━━━━━━━━━━━━━━
Bigger server                   More servers (Sharding)
[Server 4GB]                    [DB1] [DB2] [DB3]
     ↓                          data split across nodes
[Server 64GB]
(has limits!)                   scales infinitely ✅
```

### 6️⃣ SQL vs NoSQL

| Feature | SQL (MySQL) | NoSQL (MongoDB) |
|---|---|---|
| Data | Structured | Flexible |
| Storage | Tables + rows | Documents |
| Consistency | Strong ✅ | Eventual |
| Transactions | ACID ✅ | Limited |
| Best For | Banking ✅ | Chat apps |

### 7️⃣ CAP Theorem
```
Every distributed system guarantees only 2 of 3:

C — Consistency  → All nodes see same data
A — Availability → System always responds  
P — Partition    → Works if network splits
    Tolerance

Banking (FinVault) → CP ✅
  Can't show wrong balance even for a second!

Social Media → AP
  Seeing old like count is acceptable
```

### 8️⃣ API Gateway
```
[Mobile]  [Web App]  [Third Party]
    \          |          /
         [API Gateway]        ← single entry point
       /       |       \
  [Auth]  [Accounts]  [Transfer]
  Service   Service    Service

Handles → Auth, Rate limiting, Logging, SSL
```

---

## 🏦 Case Study — Design Banking System

### Requirements
```
Functional:
→ User registration and login
→ Create accounts (Savings, Current, FD)
→ Transfer money between accounts
→ View transaction history with pagination
→ Audit logging for all actions

Non-Functional:
→ 99.9% uptime
→ ACID transactions (no double debit)
→ Secure (JWT + BCrypt + HTTPS)
→ Scalable to 1M users
```

### High Level Design
```
[React Frontend]
       ↓
[API Gateway / Load Balancer]
       ↓
[Spring Boot REST API]
  ├── Auth Service     → JWT tokens
  ├── Account Service  → CRUD operations
  ├── Transfer Service → @Transactional
  └── Audit Service    → log everything
       ↓
[MySQL Database]    [Redis Cache]
```

### Database Design
```
users ──────→ accounts ──────→ transactions
                            → transfers
users ──────→ audit_logs
users ──────→ roles (many-to-many)
```

### Key Design Decisions

| Question | Decision | Why |
|---|---|---|
| SQL or NoSQL? | MySQL (SQL) | Banking needs ACID |
| Monolith or Microservices? | Monolith first | Simple, evolve later |
| How to prevent double transfer? | @Transactional + idempotency key | Atomicity |
| How to secure? | JWT + BCrypt + RBAC | Industry standard |
| Cache what? | Account balance | Most read, rarely written |

---

## 🔧 LLD — Low Level Design

### Common Interview Questions

| Question | Key Classes |
|---|---|
| Design ATM | ATM, Card, Account, Transaction, CashDispenser |
| Design Parking Lot | ParkingLot, Floor, Slot, Vehicle, Ticket |
| Design Library | Library, Book, Member, BorrowRecord |
| Design FinVault | User, Account, Transaction, Transfer, AuditLog |

### OOP Design Approach
```
Step 1 → Identify entities (classes)
Step 2 → Define relationships (has-a, is-a)
Step 3 → Add attributes and methods
Step 4 → Apply design patterns
Step 5 → Draw class diagram
```

---

## 🎨 Design Patterns

| Pattern | What It Does | Spring Example |
|---|---|---|
| Singleton | Only one instance | Spring Beans |
| Factory | Create objects dynamically | BeanFactory |
| Builder | Build complex objects | Lombok @Builder |
| Observer | Notify on state change | Spring Events |
| Proxy | Wrap to add behavior | @Transactional, AOP |
| MVC | Separate concerns | Spring MVC |
| Repository | Abstract data access | Spring Data JPA |


