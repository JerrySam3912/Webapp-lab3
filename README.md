# Lab 03 – JavaScript Fundamentals  
**Tasks:** 1.1 – Interactive Form Validator | 1.2 – Dynamic Shopping Cart  

---

## 🧩 Task 1.1 – Interactive Form Validator

### 🎯 Overview
This task builds a **real-time form validation system** similar to those used on signup pages for Facebook, Gmail, or Amazon.  
The form checks user input instantly, provides visual feedback (red/green borders), and enables the submit button only when all fields are valid.

### 💻 Features Implemented
- Username validation (4–20 characters, alphanumeric only)
- Email format validation (regex)
- Password validation (min 8 chars, one uppercase, one number)
- Confirm password check (must match)
- Live input validation (using `input` event)
- Error messages + green/red borders
- Submit button enabled only when all inputs are valid

### 🧠 Code Flow Explanation

#### 1️⃣ When the page first loads
- The script adds `input` event listeners to all 4 fields: `username`, `email`, `password`, and `confirmPassword`.
- It also adds a `submit` event listener to the form.
- The **Sign Up** button is initially **disabled**.

#### 2️⃣ When typing (real-time validation)
Whenever you type in any field, the browser triggers an `input` event → calls **`validateForm()`**.

`validateForm()` performs these steps:
1. Collects current values from all inputs.
2. Validates each field in order:
   - **Username:** must be 4–20 alphanumeric characters.  
     - Valid → green border (`.valid`), clears error message.  
     - Invalid → red border (`.invalid`), shows error message.
   - **Email:** must match email regex.
   - **Password:** must match regex `^(?=.*[A-Z])(?=.*\d).{8,}$` (≥8 chars, 1 uppercase, 1 number).
   - **Confirm Password:** must match the password exactly.
3. After all checks, if every field passes → enables the **Sign Up** button.  
   Otherwise, it remains disabled.

#### 3️⃣ Example Scenarios
- **Empty username:**  
  - Calls `validateUsername()` → fails → runs `showError('username')` → red border + error text.
- **Invalid email:**  
  - Calls `validateEmail()` → fails → runs `showError('email')`.
- **All fields valid:**  
  - Every validation passes → `clearError()` for all fields → all green borders → button enabled.

#### 4️⃣ On form submission
When clicking **Sign Up**:
- `submit` event triggers → `preventDefault()` stops page reload.
- Shows alert ✅ “Form submitted successfully!”
- Resets all input fields and disables the button again.

#### 5️⃣ Function relationships

```
[User types]
  ↳ input event
      ↳ validateForm()
          ↳ validateUsername(), validateEmail(), validatePassword(), validatePasswordMatch()
          ↳ showError() / clearError()
          ↳ Enable/disable button

[User clicks Sign Up]
  ↳ submit event
      ↳ preventDefault()
      ↳ alert("Form submitted successfully!")
      ↳ Reset form, clear classes, disable button
```

---

## 🛒 Task 1.2 – Dynamic Shopping Cart

### 🎯 Overview
This task simulates a real-world **shopping cart system** like Amazon or Shopee.  
You can add products, adjust quantities, remove items, and see total prices update automatically.

### 💻 Features Implemented
- Display product grid (4 products with emoji icons)
- “Add to Cart” button for each product
- Cart view (toggle show/hide)
- Quantity increase/decrease buttons
- Remove and Clear Cart buttons
- Automatic subtotal and total calculation
- Real-time badge count (🛒)
- Simple responsive UI

### 🧠 Code Flow Explanation

#### 1️⃣ When the page loads
- `renderProducts()` displays all products from the array `products[]`.
- `renderCart()` shows the message “Your cart is empty...” because `cart = []` initially.
- The badge on 🛒 shows **0**.

#### 2️⃣ When clicking “Add to Cart”
Example: user clicks **Add to Cart** for “Laptop”.
- Calls `addToCart(1)`:
  1. Finds product in `products[]`.
  2. Checks if it already exists in `cart`.
     - If yes → increase `qty`.
     - If no → `push()` new item `{ productId, name, price, qty: 1 }`.
  3. Calls `updateCartUI()`:
     - Updates badge count (`getCartCount()`).
     - Calls `renderCart()` to display the updated list.
     - Calculates total (`calculateTotal()`).

**Result:**  
🛒 badge shows “1”, and the cart (if open) lists “Laptop – $999.99”.

#### 3️⃣ When clicking the 🛒 Cart icon
- Calls `toggleCart()`.
- Shows or hides the `.cart-section` by changing `display` between `none` and `block`.
- Does **not** affect the data inside the cart.

#### 4️⃣ When clicking “+” (increase quantity)
- Calls `updateQuantity(productId, +1)`:
  1. Finds the item in `cart`.
  2. Increases `qty` by 1.
  3. Calls `updateCartUI()` → recalculates subtotal and total.
- **UI updates instantly:** quantity increases, subtotal and total increase, badge count updates.

#### 5️⃣ When clicking “−” (decrease quantity)
- Calls `updateQuantity(productId, -1)`:
  - Decreases `qty` by 1.
  - If quantity ≤ 0 → removes the item from `cart`.
  - Calls `updateCartUI()` again to refresh the display.

**If it was the only item:**  
The cart becomes empty → message “Your cart is empty...”, total resets to $0.00, badge shows 0.

#### 6️⃣ When clicking “Remove”
- Calls `removeFromCart(productId)`:
  - Filters out the item from `cart`.
  - Calls `updateCartUI()` → re-renders cart and recalculates totals.

#### 7️⃣ When clicking “Clear Cart”
- Calls `clearCart()`:
  - Sets `cart = []`.
  - Calls `updateCartUI()` → clears everything and resets total to 0.

#### 8️⃣ Data Structure Behind the Cart
```js
cart = [
  { productId: 1, name: 'Laptop', price: 999.99, qty: 2 },
  { productId: 3, name: 'Headphones', price: 199.99, qty: 1 }
];
```

#### 9️⃣ Why the UI always stays accurate
Every operation that changes `cart` calls:
```js
updateCartUI() {
   update badge → #cartCount
   renderCart() → rebuild cart item list
   calculateTotal() → update #cartTotal
}
```
So the **visual state (DOM)** always matches the **data state (cart array)** — no mismatches.

---

### 🧭 Complete User Flow Summary

```
[Page Load]
 ├─ renderProducts() → display all items
 └─ renderCart() → show empty cart message

[Click “Add to Cart”]
 └─ addToCart(id)
      ↳ push new item / increase qty
      ↳ updateCartUI()

[Click “+” or “−”]
 └─ updateQuantity(id, change)
      ↳ adjust qty / remove if 0
      ↳ updateCartUI()

[Click “Remove”]
 └─ removeFromCart(id)
      ↳ filter item out
      ↳ updateCartUI()

[Click “Clear Cart”]
 └─ clearCart()
      ↳ empty the cart
      ↳ updateCartUI()

[Click “🛒 Cart”]
 └─ toggleCart()
      ↳ show/hide cart section
```

---

### 🧩 Edge Cases
- Clicking “−” when quantity = 1 → removes the item.
- Adding the same product twice → increases quantity, doesn’t duplicate item lines.
- Cart badge always equals total quantity of all items.
- Hiding/showing cart does not affect the data.

---

### ✅ Example Scenario (as a customer)
1. You open the page → see 4 products and 🛒 badge = 0.  
2. You click **Add to Cart** for “Laptop” → badge = 1, Laptop appears in the cart.  
3. You click “+” → quantity = 2, total updates to $1999.98.  
4. You click “−” → quantity = 1, total back to $999.99.  
5. You click “Remove” → Laptop disappears, badge = 0.  
6. You click **Clear Cart** → all items gone, total = $0.00.  
Everything updates instantly without reloading.

---

## 📚 Key Takeaways
- Both tasks emphasize **DOM manipulation** and **state management** using plain JavaScript.
- The `cart` array (Task 1.2) and `state` object (Task 1.1) act as single sources of truth.
- Each user action triggers a function → updates data → re-renders UI.
- No page reloads — everything happens dynamically.

---

**Author:** [Hoang Xuan Dung]  
**Course:** Web application Development Laboratory 

