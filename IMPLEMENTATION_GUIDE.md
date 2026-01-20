# State Management Implementation Guide

## Quick Start

### 1. Installation
```bash
npm install
npm start
```

### 2. Basic Usage

#### Using AuthContext
```javascript
import { useAuth } from './context/AuthContext';

const MyComponent = () => {
  const { isLoggedIn, userName, role, login, logout } = useAuth();

  return (
    <div>
      {isLoggedIn ? (
        <>
          <p>Welcome {userName}!</p>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <button onClick={() => login('User', 'admin')}>Login</button>
      )}
    </div>
  );
};
```

#### Using Redux Products
```javascript
import { useSelector, useDispatch } from 'react-redux';
import { addProduct, removeProduct } from './store/slices/productsSlice';

const Products = () => {
  const dispatch = useDispatch();
  const { products } = useSelector(state => state.products);

  return (
    <div>
      {products.map(p => (
        <div key={p.id}>
          <h3>{p.name}</h3>
          <button onClick={() => dispatch(removeProduct(p.id))}>Delete</button>
        </div>
      ))}
    </div>
  );
};
```

---

## Workflow Patterns

### Pattern 1: Role-Based Rendering

```javascript
const AdminPanel = () => {
  const { role, isLoggedIn } = useAuth();

  if (!isLoggedIn || role !== 'admin') {
    return <p>Admin access required</p>;
  }

  return <div>Admin content</div>;
};
```

### Pattern 2: Form with Context and Redux

```javascript
const AddProductForm = () => {
  const { role } = useAuth();
  const dispatch = useDispatch();
  const [name, setName] = useState('');

  if (role !== 'admin') return null;

  const handleSubmit = (e) => {
    e.preventDefault();
    dispatch(addProduct({ name, price: 99, stock: 10 }));
    setName('');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button type="submit">Add Product</button>
    </form>
  );
};
```

### Pattern 3: Conditional Features Based on Cart

```javascript
const CheckoutButton = () => {
  const { cartItems } = useSelector(state => state.products);

  return (
    <button disabled={cartItems.length === 0}>
      Checkout ({cartItems.length} items)
    </button>
  );
};
```

---

## Common Tasks

### Task 1: Create New Context

**File: `src/context/NewContext.jsx`**

```javascript
import React, { createContext, useState, useCallback } from 'react';

export const NewContext = createContext();

export const NewProvider = ({ children }) => {
  const [state, setState] = useState(initialValue);

  const updateState = useCallback((newValue) => {
    setState(newValue);
  }, []);

  const value = { state, updateState };

  return (
    <NewContext.Provider value={value}>
      {children}
    </NewContext.Provider>
  );
};

export const useNewContext = () => {
  const context = React.useContext(NewContext);
  if (!context) {
    throw new Error('useNewContext must be used within NewProvider');
  }
  return context;
};
```

### Task 2: Create New Redux Slice

**File: `src/store/slices/newSlice.js`**

```javascript
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  data: [],
};

const newSlice = createSlice({
  name: 'new',
  initialState,
  reducers: {
    addItem: (state, action) => {
      state.data.push(action.payload);
    },
    removeItem: (state, action) => {
      state.data = state.data.filter(item => item.id !== action.payload);
    },
  },
});

export const { addItem, removeItem } = newSlice.actions;
export default newSlice.reducer;
```

**Update store (add to `src/store/index.js`):**
```javascript
import newReducer from './slices/newSlice';

export const store = configureStore({
  reducer: {
    products: productsReducer,
    new: newReducer,  // Add this line
  },
});
```

### Task 3: Create New Component Using Both

**File: `src/components/Combined.jsx`**

```javascript
import { useAuth } from '../context/AuthContext';
import { useSelector, useDispatch } from 'react-redux';
import { addProduct } from '../store/slices/productsSlice';

const Combined = () => {
  const { isLoggedIn, role } = useAuth();
  const { products } = useSelector(state => state.products);
  const dispatch = useDispatch();

  const handleAction = () => {
    if (isLoggedIn && role === 'admin') {
      dispatch(addProduct({ name: 'New', price: 99, stock: 10 }));
    }
  };

  return (
    <div>
      <h2>User: {isLoggedIn ? 'Logged In' : 'Guest'}</h2>
      <p>Products: {products.length}</p>
      <button onClick={handleAction}>Take Action</button>
    </div>
  );
};

export default Combined;
```

---

## Testing Scenarios

### Scenario 1: Admin Product Management

1. Open app
2. Login as Admin
3. Click "Add Product"
4. Fill form and submit
5. New product appears in list
6. Click "Edit" to modify
7. Click "Delete" to remove

### Scenario 2: User Shopping

1. Open app
2. Login as User (or Guest)
3. Click "Add to Cart" on products
4. View cart with items
5. Adjust quantities
6. Remove items
7. Clear cart

### Scenario 3: Role-Based Access

1. Login as User
   - No "Add Product" button visible
   - Cannot see Edit/Delete buttons
   - Can add to cart
2. Logout
3. Login as Admin
   - "Add Product" button appears
   - Can add/edit/delete products
   - Can also add to cart

---

## Debugging Tips

### 1. Context Debugging
```javascript
// Add console logs in AuthContext
console.log('Auth state:', authState);

// Or use React DevTools to inspect context
```

### 2. Redux Debugging
```javascript
// Install Redux DevTools browser extension
// State changes will appear in browser console

// Or add logging to reducers
reducers: {
  addProduct: (state, action) => {
    console.log('Before:', state.products.length);
    state.products.push(action.payload);
    console.log('After:', state.products.length);
  },
}
```

### 3. Component Rendering
```javascript
// Check if hooks are called properly
const MyComponent = () => {
  console.log('Rendering MyComponent');
  const { data } = useAuth();
  console.log('Auth data:', data);
  return <div>{data}</div>;
};
```

---

## State Flow Diagrams

### Authentication Flow
```
Login Form Input
    ↓
validate input
    ↓
call login() from useAuth()
    ↓
AuthContext state updates
    ↓
all components using useAuth() re-render
    ↓
LoginForm shows user info
Dashboard shows features
ProductList shows admin controls if admin
```

### Product Management Flow
```
User clicks "Add Product"
    ↓
Form validation
    ↓
dispatch(addProduct(...))
    ↓
Redux reducer processes action
    ↓
Redux state updated (immutably)
    ↓
useSelector detects state change
    ↓
ProductList component re-renders
    ↓
New product visible in grid
```

### Shopping Cart Flow
```
User clicks "Add to Cart"
    ↓
Check if logged in (useAuth)
    ↓
dispatch(addToCart(productId))
    ↓
Redux finds product and updates:
  - stock decreases
  - item added to cartItems (or qty++)
    ↓
useSelector triggers re-render
    ↓
Cart component shows item
ProductList shows updated stock
```

---

## Performance Optimization

### 1. Memoize Context Values
```javascript
const value = useMemo(
  () => ({ ...authState, login, logout }),
  [authState, login, logout]
);
```

### 2. Memoize Selectors
```javascript
const selectCartTotal = (state) =>
  state.products.cartItems.reduce((sum, item) => sum + item.price, 0);

const cartTotal = useSelector(selectCartTotal);
```

### 3. Split Components
Keep context consumers separate from large components to minimize re-renders.

---

## Troubleshooting Common Issues

### Issue: "useAuth must be used within AuthProvider"
**Solution:** Ensure your component tree has AuthProvider wrapper:
```javascript
<Provider store={store}>
  <AuthProvider>
    <App />
  </AuthProvider>
</Provider>
```

### Issue: Redux state not updating
**Solution:** Check that:
1. Action is dispatched correctly
2. Reducer handles the action
3. Component uses useSelector to subscribe
4. Slice is added to store reducer config

### Issue: Component not re-rendering
**Solution:** Verify:
1. State dependency is correct
2. Object/array references changed (immutability)
3. Component is not memoized unnecessarily
4. useCallback dependencies are correct

---

## Best Practices

1. **Use Context for:** Authentication, theme, user preferences (small, infrequent updates)
2. **Use Redux for:** Complex shared state, products, cart, global UI state
3. **Component Structure:** Container → Presenter pattern
4. **Error Handling:** Use try-catch in side effects
5. **Performance:** Profile with React DevTools before optimizing

