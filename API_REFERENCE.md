# API & Component Reference

## Context API: AuthContext

### Overview
The AuthContext provides authentication state management across the application using React's Context API.

### Location
`src/context/AuthContext.jsx`

### Context Shape

```javascript
{
  isLoggedIn: boolean,        // Whether user is authenticated
  userName: string,           // Current user's name
  role: 'user' | 'admin',    // User's role
  token: string | null,      // Authentication token
  login: (userName, role) => void,    // Login function
  logout: () => void,                 // Logout function
}
```

### Exports

#### 1. AuthContext
The context object itself (usually used with custom hooks).

#### 2. AuthProvider
Wrapper component to provide auth context to child components.

```javascript
<AuthProvider>
  <App />
</AuthProvider>
```

#### 3. useAuth() Hook
Custom hook to access auth context. Throws error if used outside AuthProvider.

```javascript
const { isLoggedIn, userName, role, login, logout } = useAuth();
```

### Functions

#### login(userName, role)
Logs in a user with given credentials.

```javascript
login('John Doe', 'admin');  // User role optional, defaults to 'user'
```

**Parameters:**
- `userName` (string): User's name
- `role` (string, optional): User role - 'user' or 'admin' (default: 'user')

**Side Effects:**
- Updates `isLoggedIn` to true
- Sets `userName`
- Sets `role`
- Generates a unique token

#### logout()
Logs out the current user.

```javascript
logout();
```

**Side Effects:**
- Sets `isLoggedIn` to false
- Clears `userName`
- Resets `role` to 'user'
- Clears `token`

---

## Redux Toolkit: productsSlice

### Overview
Redux slice managing product catalog and shopping cart state.

### Location
`src/store/slices/productsSlice.js`

### State Shape

```javascript
{
  products: [
    {
      id: number,
      name: string,
      price: number,
      stock: number,
    },
    ...
  ],
  cartItems: [
    {
      id: number,
      name: string,
      price: number,
      stock: number,
      quantity: number,
    },
    ...
  ],
}
```

### Actions

#### addProduct(productData)
Adds a new product to the catalog.

```javascript
dispatch(addProduct({
  name: 'New Product',
  price: 99.99,
  stock: 50,
}));
```

**Payload:**
- `name` (string): Product name
- `price` (number): Product price
- `stock` (number): Initial stock quantity

**Generates:** Auto-incremented ID based on timestamp

#### updateProduct(productUpdate)
Updates an existing product.

```javascript
dispatch(updateProduct({
  id: 1,
  name: 'Updated Name',
  price: 79.99,
  stock: 100,
}));
```

**Payload:**
- `id` (number): Product ID (required)
- `name` (string, optional): New product name
- `price` (number, optional): New price
- `stock` (number, optional): New stock quantity

**Note:** Only provided fields are updated

#### removeProduct(id)
Removes a product from the catalog.

```javascript
dispatch(removeProduct(1));
```

**Payload:** `id` (number) - Product ID to delete

#### addToCart(productId)
Adds a product to shopping cart or increases quantity.

```javascript
dispatch(addToCart(1));
```

**Payload:** `productId` (number) - Product ID to add

**Behavior:**
- Finds product by ID
- If product exists in cart, increases quantity
- If new to cart, adds with quantity 1
- Decreases product stock by 1 (only if stock > 0)

#### removeFromCart(productId)
Removes an item completely from cart.

```javascript
dispatch(removeFromCart(1));
```

**Payload:** `productId` (number)

**Behavior:**
- Removes item from cartItems
- Returns item's quantity back to product stock

#### updateCartQuantity({ id, quantity })
Updates quantity of item in cart.

```javascript
dispatch(updateCartQuantity({ id: 1, quantity: 5 }));
```

**Payload:**
- `id` (number): Cart item product ID
- `quantity` (number): New quantity

**Behavior:**
- Updates cart item quantity
- Adjusts product stock based on quantity difference

#### clearCart()
Empties the entire shopping cart.

```javascript
dispatch(clearCart());
```

**Behavior:**
- Returns all cart items' quantities to product stock
- Clears cartItems array

---

## React Components

### LoginForm Component

**Location:** `src/components/LoginForm.jsx`

**Props:** None (uses AuthContext)

**Features:**
- User input form for login
- Role selection (User/Admin)
- Shows login status after authentication
- Logout button

**Hooks Used:**
- `useAuth()`: Access authentication state

**Example:**
```javascript
import LoginForm from './components/LoginForm';

<LoginForm />
```

---

### Dashboard Component

**Location:** `src/components/Dashboard.jsx`

**Props:** None (uses AuthContext)

**Features:**
- Displays current user information
- Shows available features based on role
- Welcome message for guests
- Feature lists for both roles

**Hooks Used:**
- `useAuth()`: Get user info and role

---

### ProductList Component

**Location:** `src/components/ProductList.jsx`

**Props:** None (uses Redux and AuthContext)

**Features:**
- Grid display of all products
- Product details (name, price, stock)
- Add to cart button for users
- **Admin only:**
  - Add product form
  - Edit button
  - Delete button
  - Product form validation

**Hooks Used:**
- `useSelector()`: Get products state
- `useDispatch()`: Dispatch product actions
- `useAuth()`: Check role for admin features
- `useState()`: Form state management

**Admin Features:**
```javascript
// Add product
dispatch(addProduct({ name, price, stock }));

// Update product
dispatch(updateProduct({ id, name, price, stock }));

// Remove product
dispatch(removeProduct(id));
```

**User Features:**
```javascript
// Add to cart
dispatch(addToCart(productId));
```

---

### Cart Component

**Location:** `src/components/Cart.jsx`

**Props:** None (uses Redux and AuthContext)

**Features:**
- Display shopping cart items
- Quantity controls (+/- buttons)
- Remove item from cart
- Clear cart functionality
- Calculate total price
- Checkout button
- Login prompt for non-authenticated users

**Hooks Used:**
- `useSelector()`: Get cart and products state
- `useDispatch()`: Dispatch cart actions
- `useAuth()`: Check login status

**Cart Operations:**
```javascript
// Update quantity
dispatch(updateCartQuantity({ id, quantity }));

// Remove item
dispatch(removeFromCart(id));

// Clear entire cart
dispatch(clearCart());
```

---

### App Component

**Location:** `src/App.jsx`

**Structure:**
- Wraps entire app with Redux Provider and AuthProvider
- Contains layout structure
- Composes all main components

**Key Elements:**
- Header with title
- Sidebar with LoginForm
- Main content area with Dashboard, ProductList, Cart
- Footer

---

## Store Configuration

**Location:** `src/store/index.js`

```javascript
import { configureStore } from '@reduxjs/toolkit';
import productsReducer from './slices/productsSlice';

export const store = configureStore({
  reducer: {
    products: productsReducer,
  },
});
```

### Usage in App
```javascript
import { Provider } from 'react-redux';
import store from './store';

<Provider store={store}>
  <App />
</Provider>
```

---

## Complete Usage Examples

### Example 1: Login and Access Admin Features

```javascript
const MyComponent = () => {
  const { login, role, isLoggedIn } = useAuth();
  const dispatch = useDispatch();

  const handleAdminLogin = () => {
    login('Admin User', 'admin');
  };

  const handleAddProduct = () => {
    dispatch(addProduct({
      name: 'New Laptop',
      price: 999.99,
      stock: 50,
    }));
  };

  return (
    <div>
      <button onClick={handleAdminLogin}>Login as Admin</button>
      {isLoggedIn && role === 'admin' && (
        <button onClick={handleAddProduct}>Add Product</button>
      )}
    </div>
  );
};
```

### Example 2: Shopping Cart Flow

```javascript
const ShoppingFlow = () => {
  const { isLoggedIn } = useAuth();
  const dispatch = useDispatch();
  const { products, cartItems } = useSelector(state => state.products);

  const handleAddToCart = (productId) => {
    if (isLoggedIn) {
      dispatch(addToCart(productId));
    }
  };

  return (
    <div>
      {products.map(product => (
        <button key={product.id} onClick={() => handleAddToCart(product.id)}>
          Add {product.name} to Cart
        </button>
      ))}
      <p>Cart items: {cartItems.length}</p>
    </div>
  );
};
```

---

## Error Handling

### AuthContext Errors
- **useAuth used outside AuthProvider**: Throws error with message
- **Login with empty username**: Form validation prevents this

### Redux Errors
- **Removing cart item with 0 stock**: Action handles gracefully
- **Updating non-existent product**: No error, action safely checks existence

---

## Performance Considerations

1. **useSelector optimization**: Use selector functions to prevent unnecessary re-renders
2. **useCallback for callbacks**: Wrapped in LoginForm and ProductList
3. **CSS Grid responsive**: Adapts to screen size efficiently
4. **Minimal re-renders**: Each component only subscribes to needed state

