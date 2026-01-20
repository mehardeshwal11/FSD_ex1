# Code Examples & Snippets

## Part A: AuthContext (Context API)

### Example 1: Simple Login Flow

```javascript
// src/components/SimpleLogin.jsx
import { useAuth } from '../context/AuthContext';

export default function SimpleLogin() {
  const { isLoggedIn, userName, login, logout } = useAuth();

  return (
    <div>
      {isLoggedIn ? (
        <>
          <p>Hello {userName}!</p>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <button onClick={() => login('Guest')}>Login</button>
      )}
    </div>
  );
}
```

### Example 2: Role-Based Rendering

```javascript
// src/components/RoleBasedUI.jsx
import { useAuth } from '../context/AuthContext';

export default function RoleBasedUI() {
  const { role, isLoggedIn } = useAuth();

  if (!isLoggedIn) {
    return <div>Please login first</div>;
  }

  return (
    <div>
      <p>Role: {role}</p>
      
      {role === 'admin' && (
        <div className="admin-panel">
          <h2>Admin Features</h2>
          <button>Manage Users</button>
          <button>View Analytics</button>
        </div>
      )}
      
      {role === 'user' && (
        <div className="user-panel">
          <h2>User Features</h2>
          <button>Browse Products</button>
          <button>View Orders</button>
        </div>
      )}
    </div>
  );
}
```

### Example 3: Protected Component Wrapper

```javascript
// src/components/ProtectedRoute.jsx
import { useAuth } from '../context/AuthContext';

export default function ProtectedRoute({ children, requiredRole }) {
  const { isLoggedIn, role } = useAuth();

  if (!isLoggedIn) {
    return <div>You must be logged in</div>;
  }

  if (requiredRole && role !== requiredRole) {
    return <div>You don't have permission to view this</div>;
  }

  return <>{children}</>;
}

// Usage:
// <ProtectedRoute requiredRole="admin">
//   <AdminPanel />
// </ProtectedRoute>
```

---

## Part B: Redux Toolkit (productsSlice)

### Example 1: Dispatching Actions

```javascript
// src/components/ProductActions.jsx
import { useDispatch } from 'react-redux';
import {
  addProduct,
  updateProduct,
  removeProduct,
} from '../store/slices/productsSlice';

export default function ProductActions() {
  const dispatch = useDispatch();

  const handleAddProduct = () => {
    dispatch(addProduct({
      name: 'New Product',
      price: 99.99,
      stock: 50,
    }));
  };

  const handleUpdateProduct = () => {
    dispatch(updateProduct({
      id: 1,
      price: 79.99,
    }));
  };

  const handleDeleteProduct = () => {
    dispatch(removeProduct(1));
  };

  return (
    <div>
      <button onClick={handleAddProduct}>Add</button>
      <button onClick={handleUpdateProduct}>Update</button>
      <button onClick={handleDeleteProduct}>Delete</button>
    </div>
  );
}
```

### Example 2: Using selectors

```javascript
// src/components/ProductDisplay.jsx
import { useSelector } from 'react-redux';

export default function ProductDisplay() {
  // Get all products
  const { products } = useSelector(state => state.products);

  // Get cart items
  const { cartItems } = useSelector(state => state.products);

  // Derived selector: total cart value
  const cartTotal = useSelector(state =>
    state.products.cartItems.reduce(
      (sum, item) => sum + item.price * item.quantity,
      0
    )
  );

  return (
    <div>
      <p>Products: {products.length}</p>
      <p>Cart Items: {cartItems.length}</p>
      <p>Total: ${cartTotal.toFixed(2)}</p>
    </div>
  );
}
```

### Example 3: Complete Product Form

```javascript
// src/components/ProductForm.jsx
import { useState } from 'react';
import { useDispatch } from 'react-redux';
import { addProduct, updateProduct } from '../store/slices/productsSlice';

export default function ProductForm({ editingProduct, onComplete }) {
  const dispatch = useDispatch();
  const [form, setForm] = useState({
    name: editingProduct?.name || '',
    price: editingProduct?.price || '',
    stock: editingProduct?.stock || '',
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({
      ...prev,
      [name]: value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    if (!form.name || !form.price || !form.stock) {
      alert('All fields required');
      return;
    }

    if (editingProduct) {
      dispatch(updateProduct({
        id: editingProduct.id,
        name: form.name,
        price: parseFloat(form.price),
        stock: parseInt(form.stock),
      }));
    } else {
      dispatch(addProduct({
        name: form.name,
        price: parseFloat(form.price),
        stock: parseInt(form.stock),
      }));
    }

    setForm({ name: '', price: '', stock: '' });
    onComplete?.();
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="name"
        value={form.name}
        onChange={handleChange}
        placeholder="Product name"
        required
      />
      <input
        type="number"
        name="price"
        value={form.price}
        onChange={handleChange}
        placeholder="Price"
        step="0.01"
        required
      />
      <input
        type="number"
        name="stock"
        value={form.stock}
        onChange={handleChange}
        placeholder="Stock"
        required
      />
      <button type="submit">
        {editingProduct ? 'Update' : 'Add'} Product
      </button>
    </form>
  );
}
```

---

## Part C: Combining Context API + Redux

### Example 1: Auth-Protected Redux Action

```javascript
// src/components/AdminProductAdd.jsx
import { useState } from 'react';
import { useDispatch } from 'react-redux';
import { useAuth } from '../context/AuthContext';
import { addProduct } from '../store/slices/productsSlice';

export default function AdminProductAdd() {
  const { role, isLoggedIn } = useAuth();
  const dispatch = useDispatch();
  const [name, setName] = useState('');

  if (!isLoggedIn || role !== 'admin') {
    return <div>Admin access required</div>;
  }

  const handleSubmit = (e) => {
    e.preventDefault();
    dispatch(addProduct({
      name,
      price: 99.99,
      stock: 50,
    }));
    setName('');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Product name"
      />
      <button type="submit">Add Product</button>
    </form>
  );
}
```

### Example 2: Role-Based Feature Toggle

```javascript
// src/components/ProductCard.jsx
import { useDispatch } from 'react-redux';
import { useAuth } from '../context/AuthContext';
import {
  addToCart,
  updateProduct,
  removeProduct,
} from '../store/slices/productsSlice';

export default function ProductCard({ product }) {
  const { isLoggedIn, role } = useAuth();
  const dispatch = useDispatch();

  const handleAddToCart = () => {
    if (isLoggedIn) {
      dispatch(addToCart(product.id));
    } else {
      alert('Please login first');
    }
  };

  const handleEdit = () => {
    // Edit logic
  };

  const handleDelete = () => {
    if (window.confirm('Delete this product?')) {
      dispatch(removeProduct(product.id));
    }
  };

  return (
    <div className="product-card">
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      <p>Stock: {product.stock}</p>

      {/* All users can add to cart if logged in */}
      {isLoggedIn && (
        <button onClick={handleAddToCart}>
          Add to Cart
        </button>
      )}

      {/* Only admins see edit/delete */}
      {role === 'admin' && isLoggedIn && (
        <>
          <button onClick={handleEdit}>Edit</button>
          <button onClick={handleDelete}>Delete</button>
        </>
      )}

      {/* Non-logged-in users see login prompt */}
      {!isLoggedIn && (
        <p>Login to add to cart</p>
      )}
    </div>
  );
}
```

### Example 3: Conditional Form Display

```javascript
// src/components/ProductManagement.jsx
import { useState } from 'react';
import { useSelector } from 'react-redux';
import { useAuth } from '../context/AuthContext';
import ProductForm from './ProductForm';
import ProductList from './ProductList';

export default function ProductManagement() {
  const { role, isLoggedIn } = useAuth();
  const [showForm, setShowForm] = useState(false);
  const { products } = useSelector(state => state.products);

  const canManage = isLoggedIn && role === 'admin';

  return (
    <div>
      {canManage && (
        <div>
          <button onClick={() => setShowForm(!showForm)}>
            {showForm ? 'Hide' : 'Show'} Add Form
          </button>
          {showForm && (
            <ProductForm onComplete={() => setShowForm(false)} />
          )}
        </div>
      )}

      <ProductList
        products={products}
        showAdminControls={canManage}
      />
    </div>
  );
}
```

---

## Part D: Common Patterns

### Pattern 1: Loading State with Redux

```javascript
// src/store/slices/productsSlice.js (enhanced)
const productsSlice = createSlice({
  name: 'products',
  initialState: {
    products: [],
    cartItems: [],
    loading: false,
    error: null,
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchProducts.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchProducts.fulfilled, (state, action) => {
        state.loading = false;
        state.products = action.payload;
      })
      .addCase(fetchProducts.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      });
  },
});
```

### Pattern 2: Async Thunk (Future Enhancement)

```javascript
// src/store/slices/productsSlice.js (with async)
import { createAsyncThunk } from '@reduxjs/toolkit';

export const fetchProducts = createAsyncThunk(
  'products/fetchProducts',
  async (_, { rejectWithValue }) => {
    try {
      const response = await fetch('/api/products');
      const data = await response.json();
      return data;
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);
```

### Pattern 3: Persisting State to localStorage

```javascript
// src/components/App.jsx (enhanced)
import { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';

export default function App() {
  const dispatch = useDispatch();
  const { products } = useSelector(state => state.products);

  // Save to localStorage
  useEffect(() => {
    localStorage.setItem('products', JSON.stringify(products));
  }, [products]);

  // Load from localStorage
  useEffect(() => {
    const saved = localStorage.getItem('products');
    if (saved) {
      try {
        const parsed = JSON.parse(saved);
        // dispatch action to restore state
      } catch (error) {
        console.error('Failed to restore state:', error);
      }
    }
  }, [dispatch]);

  return <div>App</div>;
}
```

### Pattern 4: Custom Hook for Products

```javascript
// src/hooks/useProducts.js
import { useDispatch, useSelector } from 'react-redux';
import {
  addProduct,
  updateProduct,
  removeProduct,
  addToCart,
  removeFromCart,
} from '../store/slices/productsSlice';

export function useProducts() {
  const dispatch = useDispatch();
  const { products, cartItems } = useSelector(state => state.products);

  return {
    products,
    cartItems,
    addProduct: (data) => dispatch(addProduct(data)),
    updateProduct: (data) => dispatch(updateProduct(data)),
    removeProduct: (id) => dispatch(removeProduct(id)),
    addToCart: (id) => dispatch(addToCart(id)),
    removeFromCart: (id) => dispatch(removeFromCart(id)),
  };
}

// Usage:
// const { products, addProduct } = useProducts();
```

### Pattern 5: Custom Hook for Authentication

```javascript
// src/hooks/useAuthRequired.js
import { useAuth } from '../context/AuthContext';
import { useNavigate } from 'react-router-dom'; // if using router

export function useAuthRequired(requiredRole = null) {
  const auth = useAuth();
  const navigate = useNavigate();

  if (!auth.isLoggedIn) {
    // Redirect or show message
    console.warn('Authentication required');
    return false;
  }

  if (requiredRole && auth.role !== requiredRole) {
    console.warn(`${requiredRole} access required`);
    return false;
  }

  return true;
}

// Usage:
// const canAccess = useAuthRequired('admin');
// if (!canAccess) return <p>Access Denied</p>;
```

---

## Part E: Best Practices

### Example 1: Proper Error Handling

```javascript
// Good
const handleSubmit = async (data) => {
  try {
    dispatch(addProduct(data));
    setMessage('Product added successfully');
  } catch (error) {
    setError(`Failed to add product: ${error.message}`);
  }
};

// Bad
const handleSubmit = (data) => {
  dispatch(addProduct(data));
  // No error handling
};
```

### Example 2: Proper State Updates

```javascript
// Good - immutable update
dispatch(updateProduct({
  id: 1,
  price: 99.99,  // Only what changed
}));

// Bad - mutating state directly
products[0].price = 99.99;

// Bad - unnecessary state update
dispatch(updateProduct({
  id: 1,
  ...product,  // Unnecessary spread
  price: 99.99,
}));
```

### Example 3: Proper Hook Usage

```javascript
// Good - dependency array
const handleClick = useCallback(() => {
  dispatch(addProduct(data));
}, [dispatch, data]);

// Bad - missing dependency
const handleClick = useCallback(() => {
  dispatch(addProduct(data));
}, []);

// Bad - unnecessary hook
const handleClick = () => {
  dispatch(addProduct(data));
};
```

### Example 4: Proper Selectors

```javascript
// Good - memoized selector
const selectProducts = useCallback(
  (state) => state.products.products,
  []
);
const products = useSelector(selectProducts);

// Acceptable - inline but simple
const products = useSelector(state => state.products.products);

// Bad - creating objects
const productData = useSelector(state => ({
  products: state.products.products,
  count: state.products.products.length,
})); // Creates new object every render
```

---

## Part F: Testing Examples

### Example 1: Testing Context

```javascript
// src/context/__tests__/AuthContext.test.js
import { renderHook, act } from '@testing-library/react';
import { AuthProvider, useAuth } from '../AuthContext';

describe('AuthContext', () => {
  it('should login user', () => {
    const { result } = renderHook(() => useAuth(), {
      wrapper: AuthProvider,
    });

    act(() => {
      result.current.login('John', 'admin');
    });

    expect(result.current.isLoggedIn).toBe(true);
    expect(result.current.userName).toBe('John');
    expect(result.current.role).toBe('admin');
  });

  it('should logout user', () => {
    const { result } = renderHook(() => useAuth(), {
      wrapper: AuthProvider,
    });

    act(() => {
      result.current.login('John', 'admin');
      result.current.logout();
    });

    expect(result.current.isLoggedIn).toBe(false);
    expect(result.current.userName).toBe('');
  });
});
```

### Example 2: Testing Redux

```javascript
// src/store/__tests__/productsSlice.test.js
import productsReducer, {
  addProduct,
  removeProduct,
  addToCart,
} from '../slices/productsSlice';

describe('productsSlice', () => {
  it('should add product', () => {
    const initialState = { products: [], cartItems: [] };
    const action = addProduct({ name: 'Laptop', price: 999, stock: 5 });
    
    const state = productsReducer(initialState, action);
    
    expect(state.products).toHaveLength(1);
    expect(state.products[0].name).toBe('Laptop');
  });

  it('should remove product', () => {
    const initialState = {
      products: [{ id: 1, name: 'Laptop', price: 999, stock: 5 }],
      cartItems: [],
    };
    const action = removeProduct(1);
    
    const state = productsReducer(initialState, action);
    
    expect(state.products).toHaveLength(0);
  });
});
```

---

## Part G: Debugging Techniques

### Technique 1: Redux Logging

```javascript
// src/store/middleware/logger.js
export const loggerMiddleware = (store) => (next) => (action) => {
  console.log('Dispatching:', action);
  const result = next(action);
  console.log('New State:', store.getState());
  return result;
};
```

### Technique 2: Context Logging

```javascript
// src/context/AuthContext.jsx (with logging)
const login = useCallback((userName, role = 'user') => {
  console.log('Login attempt:', { userName, role });
  const token = `token_${Date.now()}`;
  setAuthState({
    isLoggedIn: true,
    userName,
    role,
    token,
  });
  console.log('Login successful:', { userName, role });
}, []);
```

### Technique 3: Component Render Tracking

```javascript
// src/hooks/useRenderCount.js
import { useRef, useEffect } from 'react';

export function useRenderCount(componentName) {
  const renderCount = useRef(0);

  useEffect(() => {
    renderCount.current += 1;
    console.log(`${componentName} rendered ${renderCount.current} times`);
  });

  return renderCount.current;
}

// Usage:
// const renders = useRenderCount('ProductList');
```

