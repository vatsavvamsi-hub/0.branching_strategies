# GitHub Flow: Pull Request Process

## Overview

GitHub Flow is a lightweight, branch-based workflow that supports teams and projects with regular deployments. It's centered around the Pull Request (PR) as the primary mechanism for code review and integration.

## Core Principles

1. **Main branch is always deployable** - The `main` branch should always be in a working state
2. **Descriptive branch names** - Create branches with descriptive names from `main`
3. **Commit often** - Push commits regularly to the remote branch
4. **Open Pull Requests early** - Start discussions and get feedback
5. **Merge after review** - Deploy and merge only after PR approval
6. **Deploy immediately** - Deploy to production from `main` after merging

---

## GitHub Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         GITHUB FLOW                              │
└─────────────────────────────────────────────────────────────────┘

    main branch (always deployable)
    ─────●─────────────────────────────────●───────────────────►
         │                                  │
         │ 1. Create branch                 │ 5. Merge PR
         │                                  │
         └──► feature-branch                │
              ─────●────●────●──────────────┘
                   │    │    │
                   │    │    └─ 4. Review & Approve
                   │    └────── 3. Open Pull Request
                   └─────────── 2. Make commits

    Steps:
    1. Create a branch from main
    2. Add commits (develop feature/fix)
    3. Open a Pull Request
    4. Discuss and review code
    5. Merge to main and deploy
```

---

## Detailed Pull Request Process

### Step 1: Create a Feature Branch

```
main
─────●──────────────────────────────────►
      │
      └──► feature/add-user-authentication
           ─────●
```

**Command:**
```bash
git checkout main
git pull origin main
git checkout -b feature/add-user-authentication
```

### Step 2: Develop and Commit

```
main
─────●──────────────────────────────────►
      │
      └──► feature/add-user-authentication
           ─────●────●────●────●
                │    │    │    │
                │    │    │    └─ Add tests
                │    │    └────── Implement login
                │    └─────────── Add auth middleware
                └──────────────── Initial setup
```

**Commands:**
```bash
# Make changes to your code
git add .
git commit -m "Add auth middleware"

git add .
git commit -m "Implement login functionality"

# Push to remote
git push origin feature/add-user-authentication
```

### Step 3: Open a Pull Request

```
┌────────────────────────────────────────────────────────┐
│                    PULL REQUEST                        │
├────────────────────────────────────────────────────────┤
│  feature/add-user-authentication → main               │
│                                                        │
│  Title: Add User Authentication System                │
│                                                        │
│  Description:                                          │
│  - Implements JWT-based authentication                │
│  - Adds login/logout endpoints                        │
│  - Includes middleware for protected routes           │
│  - Tests included                                      │
│                                                        │
│  Reviewers: @tech-lead, @senior-dev                   │
│  Labels: enhancement, security                        │
└────────────────────────────────────────────────────────┘
```

### Step 4: Review Process

```
┌─────────────────────────────────────────────────────────┐
│                    CODE REVIEW CYCLE                    │
└─────────────────────────────────────────────────────────┘

Developer          Reviewer           CI/CD
    │                  │                 │
    ├─ Open PR ───────►│                 │
    │                  │                 │
    │                  ├─ Trigger ──────►│
    │                  │                 │
    │                  │            Run Tests
    │                  │            Linting
    │                  │            Security Scan
    │                  │                 │
    │                  │◄─── Results ────┤
    │                  │                 │
    │◄── Comments ─────┤                 │
    │   Suggestions    │                 │
    │                  │                 │
    ├─ Make changes ──►│                 │
    ├─ Push commits ──►│                 │
    │                  │                 │
    │                  ├─ Re-trigger ───►│
    │                  │                 │
    │◄─── Approve ─────┤                 │
    │                  │                 │
```

**Review Activities:**
- Code quality check
- Security review
- Performance considerations
- Test coverage verification
- Documentation review
- Automated CI/CD checks

### Step 5: Merge and Deploy

```
Before Merge:
main
─────●──────────────────────────────────►
      │
      └──► feature/add-user-authentication
           ─────●────●────●────●

After Merge:
main
─────●─────────────────────●────────────►
      │                    │
      │                    └─ Merged: Add User Authentication
      │
      └──► feature/add-user-authentication (can be deleted)
           ─────●────●────●────● (merged)
```

**Commands:**
```bash
# Via GitHub UI: Click "Merge Pull Request"
# Or via command line:
git checkout main
git pull origin main
git merge --no-ff feature/add-user-authentication
git push origin main

# Delete the feature branch
git branch -d feature/add-user-authentication
git push origin --delete feature/add-user-authentication
```

---

## Practical Example: Adding a New Feature

### Scenario
You're working on an e-commerce application and need to add a shopping cart feature.

### Step-by-Step Implementation

#### 1. Create Branch
```bash
git checkout main
git pull origin main
git checkout -b feature/shopping-cart
```

#### 2. Develop the Feature

**File: `src/cart/cartService.js`**
```javascript
export class CartService {
  constructor() {
    this.items = [];
  }

  addItem(product, quantity) {
    const existingItem = this.items.find(item => item.product.id === product.id);
    if (existingItem) {
      existingItem.quantity += quantity;
    } else {
      this.items.push({ product, quantity });
    }
  }

  removeItem(productId) {
    this.items = this.items.filter(item => item.product.id !== productId);
  }

  getTotal() {
    return this.items.reduce((sum, item) => 
      sum + (item.product.price * item.quantity), 0
    );
  }
}
```

**Commit the service:**
```bash
git add src/cart/cartService.js
git commit -m "Add CartService with add/remove/total methods"
```

**File: `src/cart/cartController.js`**
```javascript
import { CartService } from './cartService.js';

export class CartController {
  constructor() {
    this.cartService = new CartService();
  }

  addToCart(req, res) {
    const { product, quantity } = req.body;
    this.cartService.addItem(product, quantity);
    res.json({ success: true, cart: this.cartService.items });
  }

  removeFromCart(req, res) {
    const { productId } = req.params;
    this.cartService.removeItem(productId);
    res.json({ success: true, cart: this.cartService.items });
  }

  getCart(req, res) {
    res.json({ 
      items: this.cartService.items,
      total: this.cartService.getTotal()
    });
  }
}
```

**Commit the controller:**
```bash
git add src/cart/cartController.js
git commit -m "Add CartController with API endpoints"
```

**File: `tests/cart.test.js`**
```javascript
import { CartService } from '../src/cart/cartService.js';

describe('CartService', () => {
  let cartService;

  beforeEach(() => {
    cartService = new CartService();
  });

  test('should add item to cart', () => {
    const product = { id: 1, name: 'Laptop', price: 999 };
    cartService.addItem(product, 1);
    expect(cartService.items.length).toBe(1);
  });

  test('should calculate total correctly', () => {
    cartService.addItem({ id: 1, price: 100 }, 2);
    cartService.addItem({ id: 2, price: 50 }, 1);
    expect(cartService.getTotal()).toBe(250);
  });

  test('should remove item from cart', () => {
    cartService.addItem({ id: 1, price: 100 }, 1);
    cartService.removeItem(1);
    expect(cartService.items.length).toBe(0);
  });
});
```

**Commit the tests:**
```bash
git add tests/cart.test.js
git commit -m "Add comprehensive tests for CartService"
```

#### 3. Push to Remote
```bash
git push origin feature/shopping-cart
```

#### 4. Open Pull Request

**PR Title:** `Feature: Add Shopping Cart Functionality`

**PR Description:**
```markdown
## Description
Implements a shopping cart system for the e-commerce platform.

## Changes
- ✨ Added `CartService` for cart operations
- ✨ Added `CartController` for API endpoints
- ✅ Added comprehensive unit tests
- 📝 Updated API documentation

## API Endpoints
- `POST /api/cart/add` - Add item to cart
- `DELETE /api/cart/:productId` - Remove item from cart
- `GET /api/cart` - Get cart contents and total

## Testing
```bash
npm test -- cart.test.js
```

## Checklist
- [x] Code follows style guidelines
- [x] Tests added and passing
- [x] Documentation updated
- [x] No breaking changes

## Related Issues
Closes #123
```

#### 5. Review Feedback Example

**Reviewer Comment:**
> Line 15 in `cartService.js`: Consider adding validation to ensure quantity is positive.

**Developer Response:**
```javascript
addItem(product, quantity) {
  if (quantity <= 0) {
    throw new Error('Quantity must be positive');
  }
  // ... rest of the code
}
```

**Commit:**
```bash
git add src/cart/cartService.js
git commit -m "Add quantity validation per review feedback"
git push origin feature/shopping-cart
```

#### 6. Approval and Merge

```
┌───────────────────────────────────────────────┐
│ ✅ All checks passed                          │
│ ✅ 2 approvals received                       │
│ ✅ No conflicts with main                     │
│                                               │
│ [ Merge Pull Request ▼ ]                     │
│   • Merge commit                              │
│   • Squash and merge                          │
│   • Rebase and merge                          │
└───────────────────────────────────────────────┘
```

**After merge:**
```bash
# Switch back to main and pull the changes
git checkout main
git pull origin main

# Delete the feature branch locally
git branch -d feature/shopping-cart
```

---

## Pull Request Best Practices

### 1. **Write Descriptive Titles**
- ❌ Bad: "Fix bug"
- ✅ Good: "Fix null pointer exception in user authentication"

### 2. **Provide Context in Description**
- What problem does this solve?
- How does it solve it?
- What are the side effects?
- Link to related issues

### 3. **Keep PRs Small and Focused**
- Aim for < 400 lines of code changes
- One feature/fix per PR
- Easier to review and less risky to merge

### 4. **Request the Right Reviewers**
- Domain experts
- Code owners
- At least 2 reviewers for critical code

### 5. **Respond to Feedback Promptly**
- Address comments within 24 hours
- Explain your decisions
- Be open to suggestions

### 6. **Use Draft PRs for Early Feedback**
```
┌────────────────────────────────────┐
│ 🚧 Draft Pull Request              │
│                                    │
│ Work in progress - early feedback  │
│ welcome on architecture decisions  │
└────────────────────────────────────┘
```

### 7. **Ensure CI/CD Passes**
- All tests pass
- Code coverage maintained
- Linting rules satisfied
- Security scans clean

---

## Common PR Workflows

### 1. **Hotfix Workflow**
```
main (production is broken!)
─────●──────────────────────────────────►
      │
      └──► hotfix/critical-security-patch
           ─────●────● (fast review & merge)
                     │
                     └─────────────────────► main
                                            (deploy immediately)
```

### 2. **Long-Running Feature Branch**
```
main
─────●────●────●────●────●────●────────►
      │         │         │
      │         │         │
feature/complex-feature
─────●────●────●────●────●────●
      │         │         │
      │         │         └─ Sync with main regularly
      │         └─────────── Regular updates
      └─────────────────── Initial work

# Keep feature branch updated
git checkout feature/complex-feature
git merge main  # or: git rebase main
```

### 3. **Multiple Dependent PRs**
```
main
─────●──────────────────────────────────►
      │
      └──► feature/database-schema
           ─────●────● (PR #1 - merged)
                     │
                     └──► feature/api-endpoints
                          ─────●────● (PR #2 - depends on #1)
                                    │
                                    └──► feature/ui-components
                                         ─────●────● (PR #3 - depends on #2)
```

---

## PR Review Checklist

### For Developers (Before Opening PR)
- [ ] Code compiles and runs locally
- [ ] All tests pass
- [ ] New tests added for new functionality
- [ ] Code follows project style guide
- [ ] No debugging code or console logs
- [ ] Documentation updated
- [ ] Branch is up to date with main
- [ ] Meaningful commit messages

### For Reviewers
- [ ] Code logic is sound
- [ ] No security vulnerabilities
- [ ] Performance considerations addressed
- [ ] Error handling is appropriate
- [ ] Code is readable and maintainable
- [ ] Tests are comprehensive
- [ ] No unnecessary complexity
- [ ] Documentation is clear

---

## Conclusion

GitHub Flow's Pull Request process provides:
- **Code Quality**: Peer review catches bugs early
- **Knowledge Sharing**: Team members learn from each other
- **Documentation**: PRs serve as development history
- **Collaboration**: Asynchronous discussion and feedback
- **Safety**: Main branch protection ensures stability

The key to successful GitHub Flow is maintaining a fast PR cycle - open PRs early, review quickly, merge often, and deploy confidently.
