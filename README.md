# State Management Demo: Context API + Redux Toolkit

A comprehensive React application demonstrating state management using both **Context API** (for authentication) and **Redux Toolkit** (for product management).

## Project Overview

This project showcases three main state management implementations:

### a) AuthContext (React Context API)
- Simple authentication state management
- Login/Logout functionality
- User role management (admin/user)
- Token storage

### b) Redux Toolkit - Products Slice
- Product catalog management
- CRUD operations (Create, Read, Update, Delete)
- Shopping cart functionality
- Inventory management

### c) Combined Application
- Integrates both Context API and Redux Toolkit
- Role-based UI rendering
- Different features based on user role

---

## Project Structure

```
EXPT1/
├── public/
│   └── index.html
├── src/
│   ├── context/
│   │   └── AuthContext.jsx          # Context API implementation
│   ├── store/
│   │   ├── index.js                 # Redux store configuration
│   │   └── slices/
│   │       └── productsSlice.js     # Products reducer (Redux Toolkit)
│   ├── components/
│   │   ├── LoginForm.jsx            # Login/Logout component
│   │   ├── LoginForm.css
│   │   ├── Dashboard.jsx            # Main dashboard
│   │   ├── Dashboard.css
│   │   ├── ProductList.jsx          # Product display & management
│   │   ├── ProductList.css
│   │   ├── Cart.jsx                 # Shopping cart
│   │   └── Cart.css
│   ├── App.jsx                      # Main application component
│   ├── App.css
│   ├── index.js                     # React DOM render
│   └── index.css                    # Global styles
└── package.json
```

---

## Implementation Details

### A) AuthContext (Context API)

**File:** [src/context/AuthContext.jsx](src/context/AuthContext.jsx)

```javascript
// Usage Example:
const { isLoggedIn, userName, role, login, logout } = useAuth();

// Login with role:
login('John Doe', 'admin');

// Logout:
logout();
```

**Features:**
- `isLoggedIn`: Boolean tracking login state
- `userName`: Current user's name
- `role`: User role ('admin' or 'user')
- `token`: Authentication token
- `login(userName, role)`: Login function
- `logout()`: Logout function

### B) Redux Toolkit - productsSlice

**File:** [src/store/slices/productsSlice.js](src/store/slices/productsSlice.js)

**State Structure:**
```javascript
{
  products: [
    { id: 1, name: 'Product', price: 99.99, stock: 5 },
    ...
  ],
  cartItems: [
    { id: 1, name: 'Product', price: 99.99, stock: 5, quantity: 2 },
    ...
  ]
}
```

**Actions:**
- `addProduct(productData)`: Add new product
- `updateProduct({ id, name, price, stock })`: Update existing product
- `removeProduct(id)`: Delete a product
- `addToCart(productId)`: Add item to cart
- `removeFromCart(productId)`: Remove item from cart
- `updateCartQuantity({ id, quantity })`: Change quantity
- `clearCart()`: Empty the shopping cart

**Usage Example:**
```javascript
const dispatch = useDispatch();
const { products, cartItems } = useSelector(state => state.products);

// Add product
dispatch(addProduct({ name: 'Laptop', price: 999.99, stock: 10 }));

// Update product
dispatch(updateProduct({ id: 1, price: 899.99 }));

// Remove product
dispatch(removeProduct(1));
```

### C) Components

#### LoginForm Component
- Login form with username and role selection
- Displays user info after login
- Logout button
- Role selection (Admin/User)

#### Dashboard Component
- Shows current user information
- Displays available features based on role
- Welcome message for non-authenticated users
- Admin-specific features list

#### ProductList Component
- Displays all products in a grid
- **Admin features:**
  - Add new products
  - Edit existing products
  - Delete products
- **User features:**
  - View products
  - Add to cart (if logged in)
  - View stock availability

#### Cart Component
- Shopping cart display
- Update item quantities
- Remove items from cart
- Clear cart
- Total price calculation
- Checkout button
- Login prompt for non-authenticated users

---

## Installation & Setup

### Prerequisites
- Node.js (v14 or higher)
- npm or yarn

### Installation Steps

```bash
# Navigate to project directory
cd EXPT1

# Install dependencies
npm install

# Start development server
npm start
```

The application will open at `http://localhost:3000`

---

## Usage Guide

### Getting Started

1. **Login as a Regular User:**
   - Enter any username
   - Select "User" role
   - Click "Login"
   - View products and add to cart

2. **Login as an Admin:**
   - Enter any username
   - Select "Admin" role
   - Click "Login"
   - Full access to product management

### Features by Role

#### Regular User
- ✓ Browse products
- ✓ Add items to cart
- ✓ Manage shopping cart
- ✓ View product details
- ✓ See stock availability

#### Admin User
- ✓ All user features
- ✓ Add new products
- ✓ Edit product details
- ✓ Delete products
- ✓ Manage inventory
- ✓ Control product listing

---

## Key Concepts Demonstrated

### 1. Context API
- Creating custom context
- Provider pattern
- Custom hooks for context consumption
- State management without Redux

### 2. Redux Toolkit
- Slice pattern for reducers
- Immutable state updates
- Action creators (auto-generated)
- Selector pattern with useSelector

### 3. Integration Patterns
- Combining multiple state management approaches
- Context for authentication
- Redux for complex product state
- Component composition with both

### 4. React Hooks
- `useContext()`: Access context values
- `useDispatch()`: Dispatch Redux actions
- `useSelector()`: Select Redux state
- `useState()`: Local component state
- `useCallback()`: Memoized callbacks

---

## Authentication Flow

```
Login Form
   ↓
[Submit with username & role]
   ↓
AuthContext.login(userName, role)
   ↓
Update AuthContext state
   ↓
Components re-render with new user info
   ↓
Role-based features become available
```

---

## Product Management Flow

```
User Action (Add/Edit/Delete)
   ↓
Dispatch Redux Action
   ↓
productsSlice reducer processes action
   ↓
Redux state updates (immutably)
   ↓
useSelector detects change
   ↓
Components re-render with new data
```

---

## Shopping Cart Flow

```
View Products
   ↓
Click "Add to Cart"
   ↓
Dispatch addToCart(productId)
   ↓
Product stock decreases
   ↓
Item added to cartItems
   ↓
Cart component updates
   ↓
User can manage quantities or remove items
```

---

## Styling

The application uses:
- **CSS Modules & CSS files** for component styling
- **CSS Grid** for responsive layouts
- **Flexbox** for component alignment
- **CSS Transitions** for smooth interactions
- **Gradient backgrounds** for modern UI

### Color Scheme
- Primary: #4CAF50 (Green)
- Secondary: #2196F3 (Blue)
- Accent: #667eea (Purple)
- Danger: #f44336 (Red)
- Warning: #FFC107 (Amber)

---

## Best Practices Implemented

1. **State Management:**
   - Context API for authentication (simple, small scope)
   - Redux Toolkit for products (complex, shared state)

2. **Component Design:**
   - Functional components with hooks
   - Proper separation of concerns
   - Reusable components

3. **Performance:**
   - useCallback for memoized functions
   - useSelector for efficient Redux selectors
   - CSS optimization

4. **Security:**
   - Token generation on login
   - Role-based access control
   - Protected admin actions

5. **User Experience:**
   - Confirmation dialogs for destructive actions
   - Clear error states
   - Responsive design
   - Visual feedback on interactions

---

## Development Tips

### Adding New Features

1. **New Redux State:**
   - Create slice in `src/store/slices/`
   - Add reducer to store config
   - Use `useSelector` and `useDispatch` in components

2. **New Context:**
   - Create in `src/context/`
   - Create custom hook for usage
   - Wrap components with provider

3. **New Component:**
   - Add to `src/components/`
   - Create accompanying CSS file
   - Export from component file

---

## Troubleshooting

### Issue: Redux actions not working
- Ensure store is wrapped with `<Provider>`
- Check dispatch syntax
- Verify action is exported from slice

### Issue: Context not updating
- Verify component is inside AuthProvider
- Use custom hook (useAuth) instead of useContext directly
- Check login/logout function calls

### Issue: Styles not applying
- Verify CSS file is imported
- Check class names match between JSX and CSS
- Clear browser cache and rebuild

---

## Technologies Used

- **React 18**: UI framework
- **Redux Toolkit**: State management
- **React-Redux**: React bindings for Redux
- **CSS3**: Styling and animations
- **JavaScript ES6+**: Modern JavaScript

---

## Future Enhancements

- [ ] Persistent storage (localStorage/sessionStorage)
- [ ] API integration for products
- [ ] Advanced filtering and search
- [ ] User profile page
- [ ] Order history
- [ ] Payment integration
- [ ] Notification system
- [ ] Dark mode
- [ ] Unit and integration tests

---

## License

This project is for educational purposes.

---

## Support

For questions or issues, refer to:
- [React Documentation](https://react.dev)
- [Redux Toolkit Documentation](https://redux-toolkit.js.org)
- [React Redux Documentation](https://react-redux.js.org)

