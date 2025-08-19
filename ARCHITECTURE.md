# ARCHITECTURE.md

This file provides comprehensive guidance for web development projects, emphasizing simplicity, maintainability, and web fundamentals.

## Core Development Philosophy

This will be an express application. State of objects will be saved on the server side as json.

### KISS (Keep It Simple, Stupid)

Simplicity should be a key goal in design. Choose straightforward solutions over complex ones whenever possible. Simple solutions are easier to understand, maintain, and debug.


### YAGNI (You Aren't Gonna Need It)

Avoid building functionality on speculation. Implement features only when they are needed, not when you anticipate they might be useful in the future.

### Design Principles

- **Dependency Inversion**: High-level modules should not depend on low-level modules. Both should depend on abstractions.
- **Open/Closed Principle**: Software entities should be open for extension but closed for modification.
- **Single Responsibility**: Each function, class, and module should have one clear purpose.
- **Fail Fast**: Check for potential errors early and throw exceptions immediately when issues occur.

## 🧱 Code Structure & Modularity

### File and Function Limits

- **Multi-File Projects**: Never create a file longer than 500 lines
- **Functions should be under 50 lines** with a single, clear responsibility
- **Line length should be max 100 characters** for readability
- **Prefer vanilla web technologies** over frameworks when simplicity is the goal

### Project Architecture

#### Simple Server Architecture
```
/
├── server.js               # Express/Node.js server
├── public/
│   ├── index.html
│   ├── styles.css
│   └── script.js
├── data/
│   └── storage.json        # File-based data storage
├── docs/
│   ├── openapi.yaml        # OpenAPI specification
│   └── api/
│       └── endpoints/      # Individual endpoint documentation
├── package.json
└── README.md
```

## 🛠️ Development Environment

### Simple Server Setup

#### Required Tools
- **Runtime**: Node.js 
- **Package Manager**: npm (comes with Node.js)  
- **Editor**: VS Code with Node.js extensions
- **Version Control**: Git for tracking changes

### Package Management (When Needed)

```bash
# Initialize new Node.js project
npm init -y

# Install dependencies
npm install express

# Install development dependencies
npm install --save-dev nodemon

# Run scripts
npm start
npm run dev
npm test

# Update packages
npm update

# Install specific Node.js version (using nvm)
nvm install --lts
nvm use --lts
```

### Development Commands (Framework Projects)

```bash
# Development server
npm run dev

# Build for production
npm run build

# Start production server
npm start

# Run tests
npm test

# Format code
npm run format

# Check linting
npm run lint
```

## 📋 Style & Conventions

### HTML/CSS/JavaScript Style Guide

#### HTML Standards
- **Use semantic HTML5 elements** (`<main>`, `<section>`, `<article>`, etc.)
- **Proper nesting and indentation** (2 spaces)
- **Accessible attributes** (`alt`, `aria-label`, proper form labels)
- **Clean, minimal markup** - avoid unnecessary divs

#### CSS Standards  
- **Mobile-first responsive design** using CSS Grid and Flexbox
- **CSS custom properties** for theming and consistency
- **BEM methodology** for class naming (when using separate CSS files)
- **Avoid !important** - use proper specificity
- **Line length: 100 characters max**

#### JavaScript Standards
- **ES6+ modern JavaScript** - avoid legacy patterns
- **const/let instead of var** - proper scoping
- **Arrow functions** for brevity and lexical this
- **Template literals** for string interpolation
- **Async/await** instead of callback pyramids
- **Semicolons optional but consistent**
- **camelCase** for variables and functions
- **PascalCase** for constructors and classes

### JSDoc Standards

Use JSDoc for all public functions, classes, and modules:

```typescript
/**
 * Calculate the discounted price for a product.
 * 
 * @param price - Original price of the product
 * @param discountPercent - Discount percentage (0-100)
 * @param minAmount - Minimum allowed final price
 * @returns Final price after applying discount
 * @throws {Error} If discountPercent is not between 0 and 100
 * @throws {Error} If final price would be below minAmount
 * 
 * @example
 * ```typescript
 * calculateDiscount(100, 20); // Returns 80
 * ```
 */
function calculateDiscount(
  price: number,
  discountPercent: number,
  minAmount: number = 0.01
): number {
  if (discountPercent < 0 || discountPercent > 100) {
    throw new Error('Discount percent must be between 0 and 100');
  }
  
  const discountedPrice = price * (1 - discountPercent / 100);
  
  if (discountedPrice < minAmount) {
    throw new Error(`Final price ${discountedPrice} is below minimum ${minAmount}`);
  }
  
  return discountedPrice;
}
```

### Naming Conventions

- **Variables and functions**: `camelCase`
- **Classes and interfaces**: `PascalCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Private properties/methods**: `#privateField` (private fields) or `_privateMethod` (convention)
- **Type aliases**: `PascalCase`
- **Enum values**: `PascalCase`
- **Files and directories**: `kebab-case` or `camelCase`

## 🧪 Testing Strategy

### Test-Driven Development (TDD)

1. **Write the test first** - Define expected behavior before implementation, create a openAPI documentation before creating the rest service, write a test first.
2. **Watch it fail** - Ensure the test actually tests something
3. **Write minimal code** - Just enough to make the test pass
4. **Refactor** - Improve code while keeping tests green
5. **Repeat** - One test at a time

### Testing Best Practices

```typescript
// Always use Vitest for modern testing
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { User } from '../user';

describe('User', () => {
  let sampleUser: User;

  beforeEach(() => {
    sampleUser = new User({
      id: 123,
      name: 'Test User',
      email: 'test@example.com',
      createdAt: new Date()
    });
  });

  // Use descriptive test names
  it('should update email when valid format provided', () => {
    const newEmail = 'newemail@example.com';
    sampleUser.updateEmail(newEmail);
    expect(sampleUser.email).toBe(newEmail);
  });

  // Test edge cases and error conditions
  it('should throw error when invalid email format provided', () => {
    expect(() => {
      sampleUser.updateEmail('not-an-email');
    }).toThrow('Invalid email format');
  });

  // Mock external dependencies
  it('should call email service when updating email', async () => {
    const emailService = vi.fn();
    const user = new User({ ...sampleUser, emailService });
    
    await user.updateEmail('new@example.com');
    
    expect(emailService).toHaveBeenCalledWith('new@example.com');
  });
});
```

### Test Organization

- Unit tests: Test individual functions/methods in isolation
- Integration tests: Test component interactions
- End-to-end tests: Test complete user workflows
- Keep test files next to the code they test (`__tests__` folders)
- Use `setup.ts` for shared test configuration

## 🚨 Error Handling

### Error Best Practices

```typescript
// Create custom error classes for your domain
class PaymentError extends Error {
  constructor(message: string, public code?: string) {
    super(message);
    this.name = 'PaymentError';
  }
}

class InsufficientFundsError extends PaymentError {
  constructor(
    public required: number,
    public available: number
  ) {
    super(`Insufficient funds: required ${required}, available ${available}`);
    this.name = 'InsufficientFundsError';
    this.code = 'INSUFFICIENT_FUNDS';
  }
}

// Use specific error handling with proper typing
async function processPayment(amount: number): Promise<PaymentResult> {
  try {
    await paymentGateway.charge(amount);
    return { success: true };
  } catch (error) {
    if (error instanceof InsufficientFundsError) {
      logger.warn('Payment failed', { error: error.message, required: error.required });
      return { success: false, reason: 'insufficient_funds' };
    }
    
    if (error instanceof PaymentError) {
      logger.error('Payment error', { error: error.message, code: error.code });
      return { success: false, reason: 'payment_error' };
    }
    
    // Unexpected error - rethrow
    throw error;
  }
}

// Use async/await with proper resource cleanup
async function withDatabaseTransaction<T>(
  operation: (db: Database) => Promise<T>
): Promise<T> {
  const db = await getConnection();
  const transaction = await db.beginTransaction();
  
  try {
    const result = await operation(db);
    await transaction.commit();
    return result;
  } catch (error) {
    await transaction.rollback();
    throw error;
  } finally {
    await db.close();
  }
}
```

### Logging Strategy

```typescript
import { createLogger, format, transports } from 'winston';

// Configure structured logging
const logger = createLogger({
  level: 'info',
  format: format.combine(
    format.timestamp(),
    format.errors({ stack: true }),
    format.json()
  ),
  transports: [
    new transports.File({ filename: 'error.log', level: 'error' }),
    new transports.File({ filename: 'combined.log' }),
    new transports.Console({
      format: format.combine(
        format.colorize(),
        format.simple()
      )
    })
  ]
});

// Log function execution for debugging
function logExecution<T extends any[], R>(
  target: any,
  propertyName: string,
  descriptor: TypedPropertyDescriptor<(...args: T) => Promise<R>>
) {
  const method = descriptor.value!;
  
  descriptor.value = async function (...args: T): Promise<R> {
    logger.debug(`Entering ${propertyName}`);
    try {
      const result = await method.apply(this, args);
      logger.debug(`Exiting ${propertyName} successfully`);
      return result;
    } catch (error) {
      logger.error(`Error in ${propertyName}`, { error });
      throw error;
    }
  };
}
```

## 🔧 Configuration Management

### Simple Configuration Approaches

#### Single HTML File Configuration
```javascript
// Configuration object in the HTML file
const CONFIG = {
  appName: 'My Todo App',
  theme: 'light',
  maxTodos: 100,
  storageKey: 'todos-data',
  backupEnabled: true
};
```

#### Node.js Environment Configuration
```javascript
// Load from environment variables or defaults
const config = {
  port: process.env.PORT || 3000,
  nodeEnv: process.env.NODE_ENV || 'development',
  apiKey: process.env.API_KEY || 'dev-key',
  logLevel: process.env.LOG_LEVEL || 'info'
};

module.exports = config;
```

## 🏗️ Data Storage and Validation

#### JSON File Storage (Node.js)
```javascript
const fs = require('fs').promises;
const path = require('path');

class FileStorage {
  constructor(filename) {
    this.filepath = path.join(__dirname, 'data', filename);
  }
  
  async load() {
    try {
      const data = await fs.readFile(this.filepath, 'utf8');
      return JSON.parse(data);
    } catch (error) {
      return []; // Return empty array if file doesn't exist
    }
  }
  
  async save(data) {
    await fs.mkdir(path.dirname(this.filepath), { recursive: true });
    await fs.writeFile(this.filepath, JSON.stringify(data, null, 2));
  }
}

// Usage
const storage = new FileStorage('todos.json');
const todos = await storage.load();
```

### Simple Data Validation

#### Basic JavaScript Validation
```javascript
function validateTodo(todo) {
  const errors = [];
  
  if (!todo.text || todo.text.trim().length === 0) {
    errors.push('Todo text is required');
  }
  
  if (todo.text && todo.text.length > 200) {
    errors.push('Todo text must be less than 200 characters');
  }
  
  if (typeof todo.completed !== 'boolean') {
    errors.push('Completed must be true or false');
  }
  
  return {
    isValid: errors.length === 0,
    errors
  };
}

// Usage
const validation = validateTodo({ text: '', completed: 'not-boolean' });
if (!validation.isValid) {
  console.log('Validation errors:', validation.errors);
}
```

## 🌐 Web Standards and Best Practices

### HTML Semantic Standards
```html
<!-- Use semantic HTML5 elements -->
<main>
  <header>
    <h1>Todo App</h1>
    <nav><!-- Navigation --></nav>
  </header>
  
  <section class="todo-section">
    <h2>My Tasks</h2>
    <form class="todo-form">
      <label for="todo-input">Add new todo:</label>
      <input type="text" id="todo-input" required>
      <button type="submit">Add</button>
    </form>
    
    <ul class="todo-list">
      <li class="todo-item">
        <input type="checkbox" id="todo-1">
        <label for="todo-1">Complete project</label>
        <button class="delete-btn">Delete</button>
      </li>
    </ul>
  </section>
</main>
```

### CSS Organization Standards
```css
/* CSS Custom Properties for theming */
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --success-color: #28a745;
  --danger-color: #dc3545;
  --font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
}

/* Mobile-first responsive design */
.todo-container {
  padding: 1rem;
  max-width: 600px;
  margin: 0 auto;
}

@media (min-width: 768px) {
  .todo-container {
    padding: 2rem;
  }
}

/* BEM methodology for class naming */
.todo-item {
  display: flex;
  align-items: center;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.todo-item__text {
  flex: 1;
  margin: 0 0.5rem;
}

.todo-item__text--completed {
  text-decoration: line-through;
  color: var(--secondary-color);
}
```

### JavaScript Organization Standards
```javascript
// Module pattern for organization
const TodoApp = {
  // Configuration
  config: {
    storageKey: 'todos',
    maxTodos: 100
  },
  
  // State management
  state: {
    todos: [],
    filter: 'all' // all, active, completed
  },
  
  // DOM references
  elements: {
    form: null,
    input: null,
    list: null
  },
  
  // Initialize app
  init() {
    this.bindElements();
    this.loadTodos();
    this.bindEvents();
    this.render();
  },
  
  // Event handlers
  bindEvents() {
    this.elements.form.addEventListener('submit', this.handleSubmit.bind(this));
  }
};
```

## 📝 Documentation Standards

### Code Documentation

- Every module should have a JSDoc comment explaining its purpose
- Public functions must have complete JSDoc documentation
- Complex logic should have inline comments with `// Reason:` prefix
- Keep README.md updated with setup instructions and examples
- Maintain CHANGELOG.md for version history

### API Documentation

#### OpenAPI Requirements

- **All REST endpoints MUST have OpenAPI documentation**
- **Main specification**: Store in `docs/openapi.yaml`
- **Individual endpoints**: Store detailed docs in `docs/api/endpoints/`
- **Documentation structure**: Follow OpenAPI 3.0+ specification
- **Validation**: Ensure all endpoints have complete request/response schemas
- **Examples**: Include realistic request/response examples for each endpoint

```typescript
import { Router, Request, Response } from 'express';

const router = Router();

/**
 * @openapi
 * /api/v1/products:
 *   get:
 *     summary: List all products
 *     description: Retrieve a paginated list of all active products
 *     parameters:
 *       - in: query
 *         name: skip
 *         schema:
 *           type: integer
 *           minimum: 0
 *         description: Number of products to skip (for pagination)
 *       - in: query
 *         name: limit
 *         schema:
 *           type: integer
 *           minimum: 1
 *           maximum: 100
 *         description: Maximum number of products to return
 *       - in: query
 *         name: category
 *         schema:
 *           type: string
 *         description: Filter by product category
 *     responses:
 *       200:
 *         description: List of products
 *         content:
 *           application/json:
 *             schema:
 *               type: array
 *               items:
 *                 $ref: '#/components/schemas/Product'
 */
router.get('/products', async (req: Request, res: Response) => {
  const { skip = 0, limit = 100, category } = req.query;
  
  // Implementation here
});
```

## ⚠️ Important Notes

- **NEVER ASSUME OR GUESS** - When in doubt, ask for clarification
- **Always verify file paths and module names** before use
- **Keep CLAUDE.md updated** when adding new patterns or dependencies
- **Test your code** - No feature is complete without tests
- **Document your decisions** - Future developers (including yourself) will thank you

---

_This document is a living guide. Update it as the project evolves and new patterns emerge._