# Quick Reference Card

## 🚀 Getting Started (30 seconds)

```bash
cd EXPT1
npm install
npm start
```

Open browser at `http://localhost:3000`

---

## 📁 File Structure

```
EXPT1/
├── src/context/AuthContext.jsx        ← Authentication
├── src/store/slices/productsSlice.js  ← Products & Cart
├── src/components/
│   ├── LoginForm.jsx
│   ├── ProductList.jsx
│   ├── Cart.jsx
│   └── Dashboard.jsx
└── 📄 Documentation (6 MD files)
```

---

## 🔑 Key Concepts

### Context API (Authentication)
```javascript
import { useAuth } from './context/AuthContext';

const { isLoggedIn, userName, role, login, logout } = useAuth();

// Login
login('John', 'admin');

// Logout
logout();
```

### Redux Toolkit (Products)
```javascript
import { useDispatch, useSelector } from 'react-redux';
import { addProduct, addToCart } from './store/slices/productsSlice';

// Get state
const { products, cartItems } = useSelector(state => state.products);

// Dispatch action
dispatch(addProduct({ name: 'Laptop', price: 999, stock: 50 }));
```

---

## 🎯 Main Features

| Feature | Context/Redux | Who | Implementation |
|---------|---------------|-----|-----------------|
| Login/Logout | Context API | Everyone | AuthContext + LoginForm |
| Auth State | Context API | App-wide | userName, role, token |
| Products | Redux | Everyone | ProductList |
| Add Product | Redux | Admin | ProductList Form |
| Edit Product | Redux | Admin | ProductList Form |
| Delete Product | Redux | Admin | ProductList Button |
| Shopping Cart | Redux | Users | Cart Component |
| Stock Sync | Redux | System | Automatic on action |
| Role Check | Context | System | useAuth() hook |

---

## 🔄 User Flows

### Guest → User → Admin
```
1. Login as User
   - Context: isLoggedIn=true, role='user'
   - Can: browse, add to cart
   
2. Logout
   - Context: cleared
   
3. Login as Admin
   - Context: isLoggedIn=true, role='admin'
   - Can: manage products + user features
```

### Shopping Cart Flow
```
User Adds Item → Redux addToCart() 
   → Stock -1, cartItem +1 
   → Components re-render 
   → Cart shows item, ProductList shows new stock
```

---

## 📊 State Shapes

### Context (AuthContext)
```javascript
{
  isLoggedIn: false,
  userName: '',
  role: 'user',
  token: null,
  login: fn,
  logout: fn
}
```

### Redux (productsSlice)
```javascript
{
  products: [
    { id, name, price, stock },
    ...
  ],
  cartItems: [
    { id, name, price, stock, quantity },
    ...
  ]
}
```

---

## 🎮 Components Quick Reference

### LoginForm
```javascript
<LoginForm />
// Input: username, role selection
// Output: User logged in, info displayed
```

### ProductList
```javascript
<ProductList />
// Shows: All products in grid
// Admin: Can add/edit/delete
// Users: Can add to cart
```

### Cart
```javascript
<Cart />
// Shows: Cart items with total
// Controls: Qty ±, Remove, Clear
// Calc: Total price, item count
```

### Dashboard
```javascript
<Dashboard />
// Shows: User welcome, role info
// Admin: Feature list for admin
// User: Feature list for user
```

---

## ⚙️ Common Actions

### Add Product (Admin)
```javascript
dispatch(addProduct({
  name: 'Monitor',
  price: 299.99,
  stock: 20
}));
```

### Add to Cart (User)
```javascript
dispatch(addToCart(productId));
// Decreases stock, adds to cartItems
```

### Update Quantity (User)
```javascript
dispatch(updateCartQuantity({ id: 1, quantity: 3 }));
// Updates quantity, adjusts stock
```

### Remove from Cart (User)
```javascript
dispatch(removeFromCart(productId));
// Removes from cart, restores stock
```

### Delete Product (Admin)
```javascript
dispatch(removeProduct(productId));
// Removes from catalog
```

---

## 🛡️ Role-Based Access

### What Admins See
- ✅ + Add Product button
- ✅ Edit button on each product
- ✅ Delete button on each product
- ✅ Full product CRUD

### What Users See
- ✅ Product list
- ✅ Add to Cart button
- ✅ Shopping cart
- ✅ Price & stock info

### What Guests See
- ✅ Product list (read-only)
- ✅ Prices & stock
- ❌ Can't add to cart
- ❌ Can't access admin features

---

## 🧪 Test Scenarios (Quick)

### Test 1: Login (1 min)
1. Enter username
2. Select role
3. Click Login
4. Verify user info appears

### Test 2: Admin (2 min)
1. Login as Admin
2. Click "Add Product"
3. Fill form and submit
4. Product appears

### Test 3: Shopping (2 min)
1. Login as User
2. Click "Add to Cart"
3. Go to cart
4. See item and total

---

## 🐛 Common Issues & Fixes

| Issue | Fix |
|-------|-----|
| `useAuth outside provider` | Wrap component with `<AuthProvider>` |
| Redux not updating | Check dispatch syntax |
| Context not updating | Verify login/logout called |
| Styles not applying | Check CSS import in JSX |
| Stock not syncing | Check cart actions in Redux |

---

## 📚 Documentation Files

| File | Content |
|------|---------|
| README.md | Overview & setup |
| API_REFERENCE.md | All APIs & functions |
| IMPLEMENTATION_GUIDE.md | How-to guide |
| TESTING_SCENARIOS.md | Test cases |
| ARCHITECTURE.md | Design patterns |
| CODE_EXAMPLES.md | Code snippets |
| DELIVERABLES.md | Project summary |

---

## 🎬 Demo Script (2 min)

```
1. Show app home
   "This is a state management demo"
   
2. Login as user
   "Using Context API for auth"
   
3. Add to cart
   "Using Redux for products"
   
4. Show cart
   "Stock syncs automatically"
   
5. Logout and login as admin
   "Role-based features"
   
6. Add product
   "Full CRUD operations"
   
7. Explain integration
   "Context + Redux = powerful combination"
```

---

## 🚨 Key Takeaways

✅ **Context API** = Simple state, authentication
✅ **Redux Toolkit** = Complex state, products
✅ **Integration** = Role-based UI, protected features
✅ **Modern React** = Hooks, functional components
✅ **Best Practices** = Immutability, separation of concerns

---

## 📞 Need Help?

1. Check [README.md](README.md) for overview
2. Check [API_REFERENCE.md](API_REFERENCE.md) for specific APIs
3. Check [CODE_EXAMPLES.md](CODE_EXAMPLES.md) for code samples
4. Check [TESTING_SCENARIOS.md](TESTING_SCENARIOS.md) for test cases

---

## 🎯 Next Steps

1. **Understand** the authentication flow
2. **Understand** the product management flow
3. **Explore** the components
4. **Read** the documentation
5. **Run** the application
6. **Test** all scenarios
7. **Modify** and extend

---

**Version:** 1.0.0 | **Date:** January 20, 2026 | **Status:** ✅ Ready

