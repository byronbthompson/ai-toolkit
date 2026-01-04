# Architecture Principles & SOLID in Full-App Workflow

## Overview

The full-app workflow now includes comprehensive architecture principles guidance in the PHASE 6 architecture design step. This ensures AI-generated code follows best practices for maintainability, testability, and scalability.

**File updated**: `full-app-02-architecture-tdd.md`

**Location**: PHASE 6: Architecture Design → "Architecture Principles & Best Practices" section (lines 698-970)

---

## What Was Added

### 1. SOLID Principles (Complete Guide)

All five SOLID principles with detailed explanations, code examples, and AI prompt tips:

#### S — Single Responsibility Principle (SRP)
- Each class has one reason to change
- Separates data, persistence, business logic
- Example: User class vs UserRepository vs EmailService

#### O — Open/Closed Principle (OCP)
- Open for extension, closed for modification
- Use interfaces/abstract classes for extensibility
- Example: Shape interface with Circle, Square implementations

#### L — Liskov Substitution Principle (LSP)
- Subtypes must be substitutable for base types
- Ensures inheritance doesn't break expectations
- Example: Bird hierarchy (FlyingBird vs Penguin)

#### I — Interface Segregation Principle (ISP)
- Clients shouldn't depend on unused interfaces
- Small, focused interfaces instead of fat ones
- Example: Workable, Eatable, Sleepable interfaces

#### D — Dependency Inversion Principle (DIP)
- Depend on abstractions, not concretions
- Use dependency injection with interfaces
- Example: UserService depends on Database interface, not MySQLDatabase

### 2. Additional Best Practices

**DRY (Don't Repeat Yourself)**
- Extract duplicated code into reusable functions
- AI excels at identifying duplication

**KISS (Keep It Simple, Stupid)**
- Prefer simple solutions over clever ones
- AI can generate simple implementations when prompted clearly

**YAGNI (You Aren't Gonna Need It)**
- Don't build features until you need them
- **Critical with AI**: Don't ask for "flexible" code without concrete needs

**Separation of Concerns**
- Separate data access, business logic, presentation
- Layered architecture pattern (controller → service → repository)

**Composition over Inheritance**
- Favor object composition over class inheritance
- More flexible, easier to test

### 3. Principle Enforcement Options

The workflow now asks users to choose their approach:

**Option A - Strict SOLID** (Enterprise/Complex Apps)
- All dependencies injected via interfaces
- Every class has single responsibility
- Full abstraction layers for external services
- **Effort**: +20-30% initial development time
- **Benefit**: Much easier to maintain, test, extend

**Option B - Pragmatic SOLID** (MVPs/Small Apps) — RECOMMENDED
- Apply SOLID where it provides clear value
- Skip abstraction for simple, stable dependencies
- Use interfaces for external services (DB, APIs)
- **Effort**: Minimal overhead
- **Benefit**: Clean code without over-engineering

**Option C - Relaxed** (Prototypes Only)
- Focus on working code first
- NOT RECOMMENDED except for throwaway prototypes

### 4. Code Organization Patterns

Language-specific folder structures showing how to organize code following SOLID:

**Python (Django/Flask/FastAPI)**:
```
/src
  /models       # Data models (ORM or Pydantic)
  /repositories # Data access layer (SRP, DIP)
  /services     # Business logic (SRP)
  /api          # Controllers/routes (thin layer)
  /utils        # Shared utilities
```

**Node.js/TypeScript (Express/NestJS)**:
```
/src
  /entities     # Domain models
  /repositories # Data access (DIP)
  /services     # Business logic (SRP)
  /controllers  # Request handlers (thin)
  /middlewares  # Cross-cutting concerns
  /types        # TypeScript interfaces (ISP)
```

**React/Next.js Frontend**:
```
/src
  /components   # UI components (SRP)
  /hooks        # Reusable logic
  /services     # API clients (DIP)
  /utils        # Pure functions
  /types        # TypeScript interfaces
```

### 5. AI Prompt Tips

Each SOLID principle includes specific tips for prompting AI agents:

- **SRP**: "Create separate classes for data, persistence, and business logic following SRP"
- **OCP**: "Design using interfaces/abstract classes so I can add new implementations without changing existing code"
- **LSP**: "Ensure subclasses can replace base class without breaking behavior"
- **ISP**: "Create small, focused interfaces instead of one large interface"
- **DIP**: "Use dependency injection with interfaces so implementations can be swapped"

---

## How It Fits Into the Workflow

### Step-by-Step Integration

1. **full-app-02-architecture-tdd.md** runs during architecture planning phase
2. After infrastructure/CI/CD decisions (PHASE 5)
3. Before presenting architecture options (PHASE 7)
4. User chooses Strict/Pragmatic/Relaxed approach
5. Decision recorded in `09_DECISIONS.md`
6. AI uses chosen approach during all milestone implementations

### Impact on Implementation

When implementing milestones (full-app-11-implement-milestone-n.md), AI will:
- Generate code following chosen principles (Strict/Pragmatic)
- Create interfaces/abstractions per DIP
- Separate concerns per SRP
- Use composition and dependency injection
- Provide refactoring suggestions if principles are violated

---

## Examples: SOLID in Practice

### Example 1: User Authentication (Pragmatic SOLID)

**Without SOLID (bad)**:
```python
class AuthController:
    def login(self, email, password):
        # Direct database access (violates DIP, SRP)
        user = mysql_db.query(f"SELECT * FROM users WHERE email='{email}'")
        if bcrypt.check(password, user.password):
            token = jwt.encode(user.id)
            send_email(user.email, "Login detected")  # Mixed responsibilities
            return token
```

**With Pragmatic SOLID (good)**:
```python
# Domain model (SRP)
class User:
    def __init__(self, id, email, password_hash):
        self.id = id
        self.email = email
        self.password_hash = password_hash

# Repository (DIP - depends on abstraction)
class UserRepository:
    def __init__(self, db: Database):
        self.db = db

    def find_by_email(self, email: str) -> Optional[User]:
        return self.db.query_one(User, email=email)

# Service (SRP - authentication logic only)
class AuthService:
    def __init__(self, user_repo: UserRepository, email_service: EmailService):
        self.user_repo = user_repo
        self.email_service = email_service

    def login(self, email: str, password: str) -> Optional[str]:
        user = self.user_repo.find_by_email(email)
        if user and bcrypt.check(password, user.password_hash):
            self.email_service.send_login_notification(user)
            return jwt.encode(user.id)
        return None

# Controller (thin layer)
class AuthController:
    def __init__(self, auth_service: AuthService):
        self.auth_service = auth_service

    def login(self, request):
        token = self.auth_service.login(request.email, request.password)
        return {"token": token} if token else {"error": "Invalid credentials"}
```

**Benefits of SOLID approach**:
- ✅ Easy to test (mock UserRepository, EmailService)
- ✅ Easy to swap database (PostgreSQL, MongoDB)
- ✅ Each class has one responsibility
- ✅ Can extend without modifying existing code

### Example 2: Payment Processing (Strict SOLID)

**Interface (ISP, OCP)**:
```typescript
interface PaymentProvider {
    charge(amount: number, customer: Customer): Promise<PaymentResult>;
    refund(transactionId: string): Promise<RefundResult>;
}
```

**Implementations (OCP)**:
```typescript
class StripeProvider implements PaymentProvider {
    async charge(amount: number, customer: Customer): Promise<PaymentResult> {
        // Stripe-specific implementation
    }
    async refund(transactionId: string): Promise<RefundResult> {
        // Stripe-specific refund
    }
}

class PayPalProvider implements PaymentProvider {
    async charge(amount: number, customer: Customer): Promise<PaymentResult> {
        // PayPal-specific implementation
    }
    async refund(transactionId: string): Promise<RefundResult> {
        // PayPal-specific refund
    }
}
```

**Service (DIP)**:
```typescript
class PaymentService {
    constructor(private provider: PaymentProvider) {}  // Depends on abstraction

    async processPayment(amount: number, customer: Customer): Promise<PaymentResult> {
        return this.provider.charge(amount, customer);
    }
}

// Easy to swap: new PaymentService(new StripeProvider())
// or: new PaymentService(new PayPalProvider())
```

**Benefits**:
- ✅ Add new payment providers without changing PaymentService
- ✅ Test with mock PaymentProvider
- ✅ Switch providers at runtime
- ✅ No coupling to specific provider

---

## AI-Assisted Development with SOLID

### How AI Helps Enforce SOLID

1. **Code Generation**: AI generates code following chosen principles automatically
   - Example: "Create a user service following DIP with repository pattern"
   - AI generates: UserService + UserRepository interface + implementations

2. **Refactoring**: AI can refactor existing code to follow SOLID
   - Example: "Refactor this class to follow SRP"
   - AI separates concerns into multiple classes

3. **Code Review**: AI can identify SOLID violations
   - Example: "Does this code follow SOLID principles?"
   - AI points out violations and suggests fixes

4. **Pattern Application**: AI applies design patterns consistently
   - Repository pattern for data access (DIP)
   - Strategy pattern for algorithms (OCP)
   - Factory pattern for object creation (DIP, OCP)

### Speedup with SOLID + AI

**Without SOLID + AI**: Manual coding, tight coupling
- Time: 10 hours to implement + 20 hours to refactor later
- Total: 30 hours

**With SOLID + AI**: AI generates clean code from start
- Time: 3 hours to implement with AI following SOLID
- Refactoring: Minimal (already follows principles)
- Total: 3-4 hours

**Savings**: 26 hours (87% faster)

---

## Decision Recording

When users choose their SOLID approach, it's recorded in `09_DECISIONS.md`:

```markdown
## Architecture Principles

**Approach**: Pragmatic SOLID

**Rationale**:
- MVP needs clean code without over-engineering
- Apply SOLID where it provides clear value
- Use interfaces for external services (DB, Stripe API)
- Skip abstraction for simple utilities

**Patterns to follow**:
- Repository pattern for database access (DIP)
- Service layer for business logic (SRP)
- Dependency injection for external services (DIP)
- Interface segregation for API contracts (ISP)

**Code organization**:
```
/src
  /models       # Domain models
  /repositories # Data access (DIP)
  /services     # Business logic (SRP)
  /api          # Controllers (thin)
```

**AI instructions**:
- Generate code following pragmatic SOLID
- Create interfaces for DB and external APIs
- Separate business logic into service layer
- Keep controllers thin (just request/response handling)
```

---

## Benefits of This Addition

### For Developers
✅ **Clear guidance**: Know exactly how to structure code
✅ **Concrete examples**: See SOLID in action with Python/TypeScript examples
✅ **AI prompt tips**: Know how to ask AI to generate SOLID code
✅ **Flexibility**: Choose strict vs pragmatic based on project needs

### For AI Agents
✅ **Clear constraints**: Know which principles to enforce
✅ **Consistent patterns**: Generate code following recorded decisions
✅ **Quality assurance**: Suggest refactoring if violations detected
✅ **Language-specific**: Apply patterns appropriate to chosen tech stack

### For Code Quality
✅ **Maintainable**: Code is easier to understand and modify
✅ **Testable**: Dependencies can be mocked/swapped
✅ **Extensible**: New features don't require changing existing code
✅ **Scalable**: Separation of concerns enables team growth

---

## Integration with Other Workflow Features

### Combines with AI Time Estimates
- Strict SOLID: +20-30% initial time, -50% maintenance time
- Pragmatic SOLID: +5-10% initial time, -30% maintenance time
- Time estimates in workflow account for chosen approach

### Combines with Infrastructure Decisions
- SOLID principles guide how to structure deployable services
- DIP enables swapping infrastructure (e.g., local DB → cloud DB)
- Separation of concerns aligns with microservices if chosen

### Combines with Testing Approach
- SOLID code is much easier to test (especially with DIP)
- Repository pattern enables test doubles
- Service layer can be tested without database

### Combines with Nested Specs Structure
- Each ticket/feature can choose different SOLID approach if needed
- Record per-feature architecture decisions in ticket-specific `09_DECISIONS.md`

---

## Best Practices for Using This Feature

### 1. Choose Pragmatic SOLID by Default
- Most projects benefit from pragmatic approach
- Avoid over-engineering for hypothetical needs
- Apply SOLID where it provides clear value

### 2. Be Consistent Within Project
- Once chosen, apply the same approach across all milestones
- Don't mix strict and relaxed in same codebase
- Record decision in 09_DECISIONS.md and reference it

### 3. Use AI Prompt Tips
- When implementing milestones, reference the AI prompt tips
- Example: "Create user authentication following DIP with repository pattern"
- AI will generate code matching chosen SOLID approach

### 4. Review Generated Code
- AI is good at following SOLID but not perfect
- Check for violations during quality gates
- Refactor if principles are violated

### 5. Document Deviations
- If you must violate SOLID (rare cases), document why
- Example: "Skipping DIP for console.log (stable, no need to abstract)"
- Record in code comments and/or LEARNINGS.md

---

## When to Use Strict vs Pragmatic

### Use Strict SOLID When:
- ✅ Enterprise application with 5+ year lifespan
- ✅ Team of 5+ developers
- ✅ Complex domain with frequent changes
- ✅ High test coverage requirements (>80%)
- ✅ Microservices architecture
- ✅ Multiple environments (dev/staging/prod)

### Use Pragmatic SOLID When:
- ✅ MVP or startup product
- ✅ Small team (1-4 developers)
- ✅ Clear, stable requirements
- ✅ Moderate test coverage (60-80%)
- ✅ Monolithic architecture
- ✅ Time-to-market is critical

### Use Relaxed When:
- ✅ Throwaway prototype
- ✅ Learning exercise
- ✅ Proof of concept (POC)
- ❌ **NOT for production code**

---

## Common SOLID Violations to Avoid

### Violation 1: God Class (SRP)
```python
# BAD: One class does everything
class OrderManager:
    def create_order(self): pass
    def send_email(self): pass
    def charge_payment(self): pass
    def update_inventory(self): pass
    def generate_invoice(self): pass
```

**Fix**: Split into OrderService, EmailService, PaymentService, InventoryService, InvoiceService

### Violation 2: Tight Coupling (DIP)
```typescript
// BAD: Depends on concrete implementation
class UserService {
    private db = new MySQLDatabase();  // Tightly coupled
}
```

**Fix**: Inject Database interface

### Violation 3: Fat Interface (ISP)
```typescript
// BAD: One interface for everything
interface UserOperations {
    login(): void;
    logout(): void;
    register(): void;
    resetPassword(): void;
    updateProfile(): void;
    deleteAccount(): void;
}
```

**Fix**: Split into AuthOperations, ProfileOperations

### Violation 4: Fragile Base Class (LSP)
```python
# BAD: Subclass changes behavior unexpectedly
class Rectangle:
    def set_width(self, w): self.width = w
    def set_height(self, h): self.height = h

class Square(Rectangle):  # Violates LSP
    def set_width(self, w):
        self.width = w
        self.height = w  # Unexpected side effect
```

**Fix**: Don't inherit Square from Rectangle (composition instead)

### Violation 5: Modification on Extension (OCP)
```python
# BAD: Must modify for new types
def calculate_discount(customer):
    if customer.type == "regular":
        return 0.05
    elif customer.type == "premium":
        return 0.10
    # Must modify this function for new customer types
```

**Fix**: Use strategy pattern with customer.get_discount_strategy()

---

## Summary

**What changed**: Added comprehensive SOLID principles and architecture patterns guidance to `full-app-02-architecture-tdd.md` PHASE 6.

**Why it matters**: Ensures AI-generated code is maintainable, testable, and scalable from the start, not just "working code."

**How to use**:
1. Run full-app workflow
2. When architecture phase asks about SOLID approach, choose Strict/Pragmatic/Relaxed
3. AI generates code following chosen principles throughout all milestones

**Impact**:
- Better code quality without extra effort
- AI enforces best practices automatically
- Reduces technical debt
- Makes code easier to maintain and extend

**Last Updated**: 2026-01-01 (reflects current AI capabilities as of Claude Sonnet 4.5)
