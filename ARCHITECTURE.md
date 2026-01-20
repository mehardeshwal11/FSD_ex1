# Architecture & Design Patterns

## Application Architecture

### High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                          React App                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────────┐         ┌─────────────────────────┐  │
│  │   Redux Provider     │         │   AuthContext Provider  │  │
│  │   (Redux Toolkit)    │         │   (Context API)         │  │
│  │                      │         │                         │  │
│  │  ┌────────────────┐  │         │  ┌─────────────────┐   │  │
│  │  │ products       │  │         │  │ isLoggedIn      │   │  │
│  │  │ cartItems      │  │         │  │ userName        │   │  │
│  │  │ actions:       │  │         │  │ role (admin)    │   │  │
│  │  │ - add/update   │  │         │  │ token           │   │  │
│  │  │ - remove       │  │         │  │ login/logout    │   │  │
│  │  │ - cart ops     │  │         │  │ functions       │   │  │
│  │  └────────────────┘  │         │  └─────────────────┘   │  │
│  └──────────────────────┘         └─────────────────────────┘  │
│           ↑                                    ↑                 │
│           │                                    │                 │
│  ┌────────┴────────────────────────────────────┴──────────────┐ │
│  │                    Components                              │ │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────┐ │ │
│  │  │LoginForm │  │Dashboard │  │ Products │  │   Cart    │ │ │
│  │  │          │  │          │  │  List    │  │           │ │ │
│  │  │(useAuth) │  │(useAuth) │  │(useRedux)│  │(useRedux) │ │ │
│  │  │          │  │          │  │(useAuth) │  │(useAuth)  │ │ │
│  │  └──────────┘  └──────────┘  └──────────┘  └───────────┘ │ │
│  └──────────────────────────────────────────────────────────┘ │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## State Management Strategy

### Context API (Authentication)

**Why Context for Auth?**
- Small, simple state
- Few frequent updates
- Used by many components
- Perfect for top-level concerns
- No reducer complexity needed

**State Flow:**

```
┌─────────────┐
│   User      │
│ Input       │
└──────┬──────┘
       │
       ▼
┌─────────────────────┐
│  Login Form         │
│  validates input    │
└──────┬──────────────┘
       │
       ▼
┌─────────────────────────────────┐
│  useAuth() hook                 │
│  calls login(user, role)        │
└──────┬──────────────────────────┘
       │
       ▼
┌─────────────────────────────────┐
│  AuthContext state updates      │
│  { isLoggedIn, userName, ... }  │
└──────┬──────────────────────────┘
       │
       ▼
┌─────────────────────────────────┐
│  All consumers re-render:       │
│  - LoginForm (shows user info)  │
│  - Dashboard (shows user)       │
│  - ProductList (show/hide btns) │
│  - Cart (checks login status)   │
└─────────────────────────────────┘
```

### Redux Toolkit (Products & Cart)

**Why Redux for Products?**
- Complex state with multiple operations
- Async potential (API integration)
- Clear action intent
- DevTools support
- Middleware support
- Large amount of shared state

**Action Types & Flow:**

```
USER ACTIONS                REDUX ACTIONS                STATE UPDATES
──────────────              ──────────────               ──────────────

Add Product Form    →   addProduct({name, price, stock})
                        ↓
                    produce new state:
                    products = [...products, newProduct]
                        
Add to Cart Button  →   addToCart(productId)
                        ↓
                    produce new state:
                    - add/update cartItems
                    - decrease product stock

Update Quantity     →   updateCartQuantity({id, qty})
                        ↓
                    produce new state:
                    - update cartItem qty
                    - update product stock difference

Remove from Cart    →   removeFromCart(productId)
                        ↓
                    produce new state:
                    - remove cartItem
                    - restore product stock

Delete Product      →   removeProduct(id)
                        ↓
                    produce new state:
                    - filter out product
```

---

## Component Hierarchy

```
App
├── Header
│   └── Title & Subtitle
├── Main Container
│   ├── Sidebar
│   │   └── LoginForm (useAuth)
│   │       ├── Login/Logout UI
│   │       ├── User Info Display
│   │       └── Role Selection
│   │
│   └── Content Area
│       ├── Dashboard (useAuth)
│       │   ├── User Welcome
│       │   ├── Current Role Display
│       │   └── Features List (role-based)
│       │
│       ├── ProductList (useAuth, useRedux)
│       │   ├── Product Grid
│       │   │   ├── Product Card (x multiple)
│       │   │   │   ├── Product Info
│       │   │   │   ├── Add to Cart Button (user only)
│       │   │   │   ├── Edit Button (admin only)
│       │   │   │   └── Delete Button (admin only)
│       │   │   └── No Products Message
│       │   │
│       │   └── Add Product Form (admin only)
│       │       ├── Name Input
│       │       ├── Price Input
│       │       ├── Stock Input
│       │       └── Submit Button
│       │
│       └── Cart (useAuth, useRedux)
│           ├── Cart Items List
│           │   ├── Cart Item (x multiple)
│           │   │   ├── Product Info
│           │   │   ├── Quantity Controls
│           │   │   ├── Item Total
│           │   │   └── Remove Button
│           │   └── Empty Message
│           │
│           └── Cart Summary
│               ├── Total Items Count
│               ├── Total Price
│               ├── Clear Cart Button
│               └── Checkout Button
│
└── Footer
    └── Copyright Text
```

---

## Data Flow Patterns

### Pattern 1: Authentication-Protected Feature

```
Component renders
    ↓
useAuth() gets current auth state
    ↓
Check: isLoggedIn && role === 'admin'?
    ↓
    ├─ YES → Render admin feature
    └─ NO  → Render "Access Denied" or nothing

User clicks login
    ↓
login(userName, role) called
    ↓
AuthContext state updates
    ↓
Component re-renders
    ↓
Now admin feature is visible
```

### Pattern 2: Product Modification

```
Admin fills form and clicks "Add Product"
    ↓
Form validation
    ↓
dispatch(addProduct({name, price, stock}))
    ↓
Redux reducer processes:
    - Create new ID (timestamp)
    - Add to products array
    - Return new state
    ↓
useSelector(state => state.products.products)
detects change
    ↓
ProductList component re-renders
    ↓
New product appears in grid
```

### Pattern 3: Shopping Cart

```
User clicks "Add to Cart"
    ↓
Check: isLoggedIn?
    ├─ NO  → Show login message
    └─ YES → dispatch(addToCart(productId))
        ↓
    Redux finds product by ID
        ↓
    Check: stock > 0?
    ├─ NO  → No action (can't add)
    └─ YES → 
        - Decrease product.stock by 1
        - Add/update cartItem
        ↓
    useSelector detects state change
        ↓
    Both ProductList AND Cart re-render:
        - ProductList: stock updated
        - Cart: item added/quantity++
```

---

## State Shape

### Redux State

```javascript
{
  products: {
    products: [
      {
        id: 1,
        name: "Laptop",
        price: 999.99,
        stock: 5
      },
      {
        id: 2,
        name: "Mouse",
        price: 29.99,
        stock: 50
      },
      ...
    ],
    cartItems: [
      {
        id: 1,
        name: "Laptop",
        price: 999.99,
        stock: 5,
        quantity: 2  // User has 2 in cart
      },
      ...
    ]
  }
}
```

### Context State

```javascript
{
  isLoggedIn: false,
  userName: "",
  role: "user",
  token: null,
  // + functions:
  login: (userName, role) => {...},
  logout: () => {...}
}
```

---

## Interaction Scenarios

### Scenario 1: Admin Adds Product → User Buys It

```
Step 1: Admin Login
  Context: isLoggedIn=true, role="admin"
  Result: Admin controls visible

Step 2: Admin Adds Product
  useDispatch() → addProduct()
  Redux: adds "Monitor" to products array
  ProductList re-renders: Monitor visible

Step 3: Admin Logout
  Context: isLoggedIn=false, role="user"
  Redux: unchanged (product persists)

Step 4: User Login
  Context: isLoggedIn=true, role="user"
  Result: User controls visible, Monitor still there

Step 5: User Adds to Cart
  useDispatch() → addToCart(monitorId)
  Redux: Monitor stock 20→19, cartItem added
  Both ProductList and Cart re-render
```

### Scenario 2: Multiple Users Different Roles

```
Timeline:
┌─────────────┬─────────────┬─────────────┬─────────────┐
│   Admin     │   User1     │   Admin     │   User2     │
│             │             │             │             │
│ Add         │ Add         │ Edit        │ View new    │
│ Product     │ to Cart     │ Product     │ price       │
│             │             │             │             │
│ State:      │ State:      │ State:      │ State:      │
│ Redux only  │ Redux +     │ Redux       │ Redux +     │
│ changes     │ Context     │ changes     │ Context     │
│             │ changes     │             │ changes     │
└─────────────┴─────────────┴─────────────┴─────────────┘

Redux: always updates (global)
Context: updates per user session
```

---

## Error Handling Architecture

```
User Action
    ↓
Form Validation
    ├─ Invalid → Show error to user (no state change)
    └─ Valid   → ↓
    ↓
Dispatch Action
    ↓
Reducer Processes
    ├─ Check preconditions
    ├─ Immutably update state
    └─ Return new state
    ↓
Selectors detect change
    ↓
Components re-render
    └─ Show updated state
```

---

## Performance Optimization Points

### 1. Selector Memoization
```javascript
// Without memoization:
useSelector(state => state.products.products)  // new reference each render

// Optimized:
const selectProducts = (state) => state.products.products;
const products = useSelector(selectProducts);  // stable reference
```

### 2. Component Splitting
```
ProductList (large)
├── ProductHeader (small, memoize)
├── ProductForm (controlled component)
└── ProductGrid
    └── ProductCard (small, memoize)
```

### 3. Callback Memoization
```javascript
const handleAddToCart = useCallback((id) => {
  dispatch(addToCart(id));
}, [dispatch]);  // Only depends on dispatch
```

---

## Testing Strategy

### Unit Tests
```
AuthContext
├── login() sets state correctly
├── logout() clears state
└── useAuth() throws outside provider

productsSlice
├── addProduct creates with new ID
├── updateProduct modifies correctly
├── removeProduct filters correctly
├── addToCart updates stock
└── cart operations maintain consistency
```

### Integration Tests
```
Auth + ProductList
├── Admin sees Add button
└── User doesn't see Add button

Redux + Components
├── Dispatch action → component updates
└── Multiple components see same state
```

### E2E Tests
```
Full flow:
Login → Add to Cart → Adjust Qty → Checkout → Logout
```

---

## Security Considerations

### Current Implementation
- ✓ Role-based UI (admin/user)
- ✓ Client-side token generation
- ✓ State isolated per context consumer

### Production Enhancements
- [ ] Server-side validation
- [ ] Secure token storage
- [ ] HTTPS for all requests
- [ ] Backend permission checks
- [ ] Encrypted sensitive data
- [ ] CSRF protection

---

## Scalability Path

### Current (Small App)
```
1 Context (Auth) + 1 Slice (Products)
```

### Medium App
```
Auth Context + 
  ├── Products Slice
  ├── Users Slice
  ├── Orders Slice
  └── Settings Context
```

### Large App
```
Redux Toolkit for everything
  ├── Auth Slice
  ├── Products Slice
  ├── Cart Slice
  ├── Orders Slice
  ├── Users Slice
  └── UI Slice

+ Redux Sagas/Thunks for side effects
+ Redux Persist for local storage
+ Redux DevTools for debugging
```

---

## Key Design Decisions

| Decision | Reason |
|----------|--------|
| Context API for Auth | Simple state, rarely changes |
| Redux for Products | Complex state, frequent updates |
| Hooks everywhere | Modern React best practice |
| Immutable updates | Predictable state changes |
| No middleware (yet) | Simple async not needed |
| CSS files per component | Scoped, maintainable styles |
| Grid layout | Responsive, modern |

---

