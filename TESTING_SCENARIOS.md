# Complete Testing & Demo Scenarios

## Getting Started

### Installation
```bash
cd EXPT1
npm install
npm start
```

The app will open at `http://localhost:3000`

---

## Test Scenario 1: Guest User Experience

**Objective:** Test features available without login

**Steps:**

1. **Initial Load**
   - [ ] App displays dashboard with "Welcome to State Management Demo"
   - [ ] Dashboard shows "Please login to access features"
   - [ ] Shows feature descriptions for both Regular Users and Administrators
   - [ ] LoginForm displays on left sidebar

2. **View Products**
   - [ ] ProductList displays 3 default products (Laptop, Mouse, Keyboard)
   - [ ] Each product shows: name, price, stock
   - [ ] "Add to Cart" buttons are visible
   - [ ] Add to Cart buttons are functional (click triggers login prompt or shows message)

3. **View Cart**
   - [ ] Cart displays "Please login to view your cart"
   - [ ] No cart items visible
   - [ ] Cart is empty

**Expected Result:** ✓ All features protected behind login

---

## Test Scenario 2: Regular User Experience

**Objective:** Test features for non-admin users

**Steps:**

1. **Login as Regular User**
   - [ ] Enter username: "John"
   - [ ] Select role: "User"
   - [ ] Click "Login"
   - [ ] LoginForm changes to show:
     - "Welcome, John!"
     - Role: USER
     - Logout button

2. **Dashboard Update**
   - [ ] Dashboard shows: "Welcome, John!"
   - [ ] Shows: "User role"
   - [ ] Lists features:
     - ✓ Browse Products
     - ✓ Add to Cart
     - ✓ Manage your shopping cart

3. **Product Features**
   - [ ] ProductList is visible
   - [ ] NO "Add Product" button appears
   - [ ] NO Edit/Delete buttons on products
   - [ ] "Add to Cart" buttons are active (not disabled)

4. **Add Products to Cart**
   - [ ] Click "Add to Cart" for Laptop ($999.99)
   - [ ] Alert shows: "Product added to cart!"
   - [ ] Laptop stock decreases from 5 to 4
   - [ ] Click "Add to Cart" for Mouse ($29.99)
   - [ ] Stock decreases from 50 to 49

5. **Cart Management**
   - [ ] Cart displays 2 items
   - [ ] Laptop: quantity 1, total $999.99
   - [ ] Mouse: quantity 1, total $29.99
   - [ ] Total Price: $1029.98

6. **Update Cart Quantities**
   - [ ] Click + button next to Laptop
   - [ ] Quantity increases to 2
   - [ ] Total for Laptop: $1999.98
   - [ ] Laptop stock decreases to 3
   - [ ] Overall Total: $2029.97

7. **Remove Items**
   - [ ] Click "Remove" on Mouse
   - [ ] Mouse removed from cart
   - [ ] Stock restored to 50
   - [ ] Only Laptop remains

8. **Clear Cart**
   - [ ] Click "Clear Cart" button
   - [ ] Confirmation dialog appears
   - [ ] After confirmation, cart is empty
   - [ ] All stock returned:
     - Laptop: 5
     - Mouse: 50

9. **Logout**
   - [ ] Click "Logout"
   - [ ] LoginForm resets to login state
   - [ ] Cart shows login message again
   - [ ] Dashboard shows login prompt

**Expected Result:** ✓ All user features work correctly

---

## Test Scenario 3: Admin User Experience

**Objective:** Test full admin capabilities

**Steps:**

1. **Login as Admin**
   - [ ] Enter username: "Admin"
   - [ ] Select role: "Admin"
   - [ ] Click "Login"
   - [ ] Status shows "Welcome, Admin!" and "Role: ADMIN"

2. **Dashboard Update**
   - [ ] Shows all user features plus:
     - ✓ Add Products
     - ✓ Edit Products
     - ✓ Delete Products
     - ✓ Manage Inventory

3. **Admin Product Controls**
   - [ ] "+ Add Product" button appears on ProductList
   - [ ] Each product shows Edit and Delete buttons
   - [ ] Products still have "Add to Cart" functionality

4. **Add New Product**
   - [ ] Click "+ Add Product"
   - [ ] Form appears with fields:
     - Product Name
     - Price
     - Stock
   - [ ] Enter:
     - Name: "Monitor"
     - Price: 299.99
     - Stock: 20
   - [ ] Click "Add Product"
   - [ ] Monitor added to product list
   - [ ] Form closes
   - [ ] Monitor appears as 4th product

5. **Edit Product**
   - [ ] Click "Edit" on Mouse product
   - [ ] Form shows current data:
     - Name: "Mouse"
     - Price: 29.99
     - Stock: 50
   - [ ] Change Price to 24.99
   - [ ] Click "Update Product"
   - [ ] Mouse price updates to $24.99 in the list
   - [ ] Form closes

6. **Delete Product**
   - [ ] Click "Delete" on Keyboard
   - [ ] Confirmation dialog: "Are you sure you want to delete this product?"
   - [ ] Click "OK"
   - [ ] Keyboard removed from list
   - [ ] Only 3 products remain: Laptop, Mouse, Monitor

7. **Add to Cart as Admin**
   - [ ] Click "Add to Cart" on Monitor
   - [ ] Monitor added to cart
   - [ ] Monitor stock decreases from 20 to 19

8. **Cart and Inventory Sync**
   - [ ] Cart shows Monitor: 1 item, $299.99
   - [ ] ProductList shows Monitor: 19 stock
   - [ ] Increase cart quantity to 2
   - [ ] Monitor stock updates to 18
   - [ ] Cart total updates to $599.98

9. **Add Products While Items in Cart**
   - [ ] Add new product:
     - Name: "SSD"
     - Price: 149.99
     - Stock: 30
   - [ ] SSD appears in product list with 30 stock
   - [ ] Cart still has Monitor (qty 2, $599.98)
   - [ ] Total: $599.98

10. **Clear Cart**
    - [ ] Click "Clear Cart"
    - [ ] Monitor stock restored to 20
    - [ ] SSD remains with 30 stock (wasn't in cart)

11. **Cleanup - Delete Test Products**
    - [ ] Delete "Monitor"
    - [ ] Delete "SSD"
    - [ ] Only original products remain: Laptop, Mouse

12. **Logout**
    - [ ] Click "Logout"
    - [ ] No Edit/Delete buttons visible
    - [ ] "+ Add Product" button disappears
    - [ ] Back to login screen

**Expected Result:** ✓ Full admin functionality works correctly

---

## Test Scenario 4: Multi-User Scenario

**Objective:** Test multiple users in sequence

**Steps:**

1. **User A (Admin)**
   - [ ] Login as "Alice" with role "Admin"
   - [ ] Add product: "Headphones" - $79.99 - 15 stock
   - [ ] Product appears in list

2. **User B (Regular User)**
   - [ ] Logout as Alice
   - [ ] Login as "Bob" with role "User"
   - [ ] Dashboard shows "Bob" and "USER"
   - [ ] Headphones product visible (added by Alice)
   - [ ] NO Edit/Delete buttons (only User role)

3. **User B Shopping**
   - [ ] Add Headphones to cart (qty 2)
   - [ ] Headphones stock: 13
   - [ ] Add Laptop to cart (qty 1)
   - [ ] Laptop stock: 4
   - [ ] Cart total: $2,079.97

4. **User A Modifies**
   - [ ] Logout as Bob
   - [ ] Login as "Alice" (Admin)
   - [ ] Edit Headphones price: $89.99
   - [ ] Price updates in list
   - [ ] Cart for Bob would show old price (local state)

5. **Stock Verification**
   - [ ] Headphones: 13 stock (correct - Bob bought 2)
   - [ ] Laptop: 4 stock (correct - Bob bought 1)

**Expected Result:** ✓ Multiple users can interact, state is persistent

---

## Test Scenario 5: Stock Management Edge Cases

**Objective:** Test stock and quantity handling

**Steps:**

1. **Out of Stock Item**
   - [ ] Login as Admin
   - [ ] Delete all default products
   - [ ] Add "Limited Item" - $50 - Stock: 1
   - [ ] Logout, Login as User

2. **Buy Last Item**
   - [ ] Click "Add to Cart" for Limited Item
   - [ ] Stock shows: 0
   - [ ] "Add to Cart" button shows "Out of Stock"
   - [ ] Button is disabled (grayed out)
   - [ ] Cannot add more

3. **Return Item to Inventory**
   - [ ] Cart shows "Limited Item" (qty 1)
   - [ ] Click "Remove" from cart
   - [ ] Stock restored to 1
   - [ ] Button shows "Add to Cart" again and is enabled

4. **Quantity More Than Stock**
   - [ ] Add "Limited Item" to cart (qty 1)
   - [ ] Try to increase qty using + button → qty 2, but stock is 0
   - [ ] Keyboard input: type "5" in quantity field
   - [ ] Value capped or handled gracefully

**Expected Result:** ✓ Stock prevents overbooking

---

## Test Scenario 6: Form Validation

**Objective:** Test input validation

**Steps:**

1. **Login Form**
   - [ ] Try to login with empty username
   - [ ] Form doesn't submit
   - [ ] Click "Login" with empty input
   - [ ] Nothing happens

2. **Add Product Form (Admin)**
   - [ ] Try to add product with empty name
   - [ ] Form doesn't submit
   - [ ] Try with name only (no price)
   - [ ] Form doesn't submit
   - [ ] Fill all fields and submit
   - [ ] Product added successfully

3. **Edit Product Form**
   - [ ] Edit a product and clear the name field
   - [ ] Try to update
   - [ ] Form doesn't submit

**Expected Result:** ✓ All forms validate properly

---

## Test Scenario 7: UI/UX Features

**Objective:** Test user interface responsiveness

**Steps:**

1. **Button States**
   - [ ] "Add to Cart" button disabled when out of stock
   - [ ] "Add Product" button only visible to admin
   - [ ] Edit/Delete buttons only visible to admin
   - [ ] Logout button appears when logged in

2. **Visual Feedback**
   - [ ] Adding to cart shows alert
   - [ ] Deleting product shows confirmation
   - [ ] Clearing cart shows confirmation
   - [ ] Products display hover effects

3. **Layout Responsiveness**
   - [ ] Open on desktop - sidebar + content layout
   - [ ] Resize window smaller
   - [ ] Layout adapts (if responsive CSS is used)
   - [ ] All buttons remain accessible

4. **Form Display**
   - [ ] Click "+ Add Product"
   - [ ] Form appears with proper styling
   - [ ] Form fields are properly labeled
   - [ ] Submit button is visible and clickable

**Expected Result:** ✓ UI is responsive and provides good feedback

---

## Test Scenario 8: State Persistence

**Objective:** Test if state is maintained during interactions

**Steps:**

1. **Add Multiple Products**
   - [ ] As Admin, add 5 products
   - [ ] All 5 appear in list

2. **Edit and Delete**
   - [ ] Edit 2 products
   - [ ] Delete 1 product
   - [ ] Remaining products show updated info

3. **Logout/Login Cycle**
   - [ ] Logout
   - [ ] Login as different user
   - [ ] All products still there (state persisted)
   - [ ] Cart is empty for new user

4. **Cart Across Sessions**
   - [ ] Add items to cart
   - [ ] Logout (note: cart clears on logout)
   - [ ] Login as same user
   - [ ] Cart is empty (expected behavior)

**Expected Result:** ✓ Product state persists, cart clears on logout

---

## Performance Testing

### Test Scenario 9: Load Multiple Products

**Objective:** Test with large product list

**Steps:**

1. **Add Many Products**
   - [ ] As Admin, add 10-15 products
   - [ ] App remains responsive
   - [ ] Products display in grid

2. **Large Cart**
   - [ ] Add 10+ items to cart
   - [ ] Cart displays all items smoothly
   - [ ] Quantity updates don't lag

3. **Edit/Delete Performance**
   - [ ] Edit products quickly
   - [ ] Delete multiple products
   - [ ] No performance degradation

**Expected Result:** ✓ App handles moderate load well

---

## Error Scenarios

### Test Scenario 10: Unexpected Interactions

**Objective:** Test robustness

**Steps:**

1. **Rapid Clicks**
   - [ ] Rapidly click "Add to Cart" multiple times
   - [ ] Items are added correctly (may need throttling)

2. **Form Spam**
   - [ ] Try to submit form rapidly
   - [ ] Single submission only

3. **Invalid Inputs**
   - [ ] Type negative price
   - [ ] Type decimal stock quantity
   - [ ] Type special characters
   - [ ] App handles gracefully

**Expected Result:** ✓ App doesn't crash, handles edge cases

---

## Summary Checklist

- [ ] **Context API (AuthContext)**
  - [ ] Login works with username and role
  - [ ] Logout clears all auth data
  - [ ] Role changes admin UI visibility
  - [ ] Token is generated and stored

- [ ] **Redux Toolkit (productsSlice)**
  - [ ] Add product works
  - [ ] Update product works
  - [ ] Delete product works
  - [ ] Add to cart updates stock
  - [ ] Remove from cart restores stock
  - [ ] Cart quantity management works
  - [ ] Clear cart works

- [ ] **Integration**
  - [ ] Auth and Redux work together
  - [ ] Role-based UI rendering works
  - [ ] Features visible only to appropriate roles
  - [ ] All components communicate properly

- [ ] **User Experience**
  - [ ] No console errors
  - [ ] Responsive design
  - [ ] Smooth interactions
  - [ ] Clear user feedback

---

## Quick Demo Script (2 minutes)

```
1. "Let me show you this state management app."
2. "First, it uses React Context API for authentication."
3. "We'll login as a regular user." [Login as User]
4. "Now we can browse products and add to cart." [Add to cart]
5. "Here's the cart with items and total." [Show cart]
6. "Let's logout and login as an admin."
7. "Admins can add, edit, and delete products." [Show add form]
8. "As an admin, I can manage the product catalog." [Add product]
9. "All state is managed with Redux Toolkit behind the scenes."
10. "Context API for auth, Redux for products - best of both worlds!"
```

