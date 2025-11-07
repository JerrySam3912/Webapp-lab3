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

# 🌦️ Task 2.1 – Weather Dashboard

### 🎯 Overview  
This task builds a **Weather Dashboard** web app that fetches **current weather** and a **5-day forecast** from the **OpenWeatherMap API**, displaying the data dynamically without reloading the page.  
It trains the ability to use:  
- **DOM manipulation** for updating UI elements  
- **Event handling** (input + button + keypress)  
- **`fetch()` with `async/await`** for asynchronous API calls  
- **JSON parsing & array processing**  
- **LocalStorage** for recent search history  
- **UI state management** (loading, error, results)

---

### 💻 Features Implemented  
- 🔍 Search weather by city name (e.g. *London*, *Ho Chi Minh*, *Tokyo*)  
- 🌤️ Display **current conditions**: temperature, humidity, wind, pressure, and emoji icon based on weather type  
- 📅 Show **5-day forecast** (1 card per day around 12:00)  
- ⏳ Show **loading indicator** and **error messages** (city not found / network issues)  
- 🕓 Save **recent searches** (top 5, clickable chips) via LocalStorage  
- 💎 Responsive UI layout (1 column on mobile, 2 columns on desktop)

---

### 🧠 Code Flow Explanation  

#### 1️⃣ When the page loads  
- Calls `init()` → renders recent search chips from LocalStorage (`renderRecentSearches()`).  
- The search input listens for **Enter** key and the button for **click** to trigger `searchWeather()`.

#### 2️⃣ When user searches a city  
- `searchWeather()` is called →  
  1. Reads `cityInput.value`.  
  2. If empty → `showError('Please enter a city name.')`.  
  3. Otherwise:  
     - Clears previous errors and UI.  
     - Calls `setLoading(true)` → shows “Loading weather data…” text and disables button.  
     - Executes `Promise.all([fetchWeather(city), fetchForecast(city)])`.  
     - On success:  
       - `displayWeather(current)` → renders current card (city, country, temp, humidity, wind, pressure + emoji).  
       - `displayForecast(forecast)` → creates 5 forecast cards.  
       - `saveRecentSearch(city)` → updates LocalStorage & re-renders chips.  
     - On error → `showError(err.message)`.  
     - Finally → `setLoading(false)`.

#### 3️⃣ Helper Functions and Logic  
- `selectEmoji(main)` → maps weather main type to emoji ☀️/☁️/🌧️/⛈️/❄️/🌫️.  
- `kmh(ms)` → convert wind speed m/s → km/h.  
- `setLoading(isLoading)` → toggles visibility and button state.  
- `saveRecentSearch(city)` / `renderRecentSearches()` → store max 5 unique (city names case-insensitive).  
- `quickSearch(city)` → fills input and triggers `searchWeather()` when a chip is clicked.  

#### 4️⃣ Forecast selection logic  
- API returns 3-hour intervals (40 items).  
- Prefer items with `dt_txt` ≈ `12:00:00` (each day).  
- If < 5 found, fill by taking every 8th item until 5 days are covered.

---

### 🧭 Complete User Flow Summary  

```
[Page Load]
 └─ init()
      ↳ renderRecentSearches()

[User types city + presses Enter or clicks Search]
 └─ searchWeather()
      ↳ validate input
      ↳ setLoading(true)
      ↳ fetchWeather() + fetchForecast()
           ↳ displayWeather()
           ↳ displayForecast()
      ↳ saveRecentSearch()
      ↳ setLoading(false)

[Click on Recent Chip]
 └─ quickSearch(city)
      ↳ sets input → searchWeather()
```

---

### 🧩 Edge Cases  
- ❌ Empty input → “Please enter a city name.”  
- ❌ Invalid city (404) → “City not found. Please check the name.”  
- ⚠️ Network error or API key problem → “Failed to fetch …”  
- ⚠️ Opened as `file:///` → Fetch blocked (CORS) → must use local server.  
- ⚠️ Recent searches > 5 → oldest removed.  

---

### ✅ Example Scenario  
1. User opens the app → recent search chips (0 or few last cities).  
2. Types **Ho Chi Minh** → presses Search.  
3. App shows “Loading weather data…” → then cards with current temp and forecast.  
4. Types **Tokyo** → adds a new chip below.  
5. Clicks old chip **Ho Chi Minh** → instantly reloads its weather.  
6. After 5+ different cities, only the latest five remain.  

---

### 🧾 Configuration and Testing  
**API Key setup:**  
```js
const API_KEY = "YOUR_OPENWEATHER_API_KEY";
```
**Testing Checklist:**  
- ✅ Valid city → renders current + 5-day forecast  
- ❌ Invalid city → error message  
- ⚡ Disconnect Internet → network error displayed  
- 🔁 >5 searches → only latest 5 shown  
- 📅 Forecast shows exactly 5 cards  

---

### 📁 File Structure  
- **App:** `task2-1-weather-dashboard-fixed.html`  
- Contains all HTML, CSS, JS logic in one file.  
- Uses Live Server to avoid CORS issues.

---

### 📚 Key Takeaways  
- Demonstrates integration with a **real-time API** (OpenWeatherMap).  
- Reinforces **asynchronous programming** and **UI state management**.  
- Uses **LocalStorage** to preserve user search history.  
- Employs clean component-like functions for rendering and error handling.  
- A solid practice of fetching data → transforming it → rendering DOM dynamically.

---

# 🔍 Task 2.2 – GitHub Repository Finder

### 🎯 Overview
This task builds a **GitHub Repository Finder** web application using the **GitHub Search API**.  
It allows users to search repositories by keywords, apply sort options, and view paginated results dynamically without page reload.  
The task focuses on mastering:  
- **Fetch API + async/await**
- **DOM manipulation** for rendering repo cards
- **Event handling** for search, sort, and pagination
- **JSON parsing** and handling query parameters
- **UX state management** (loading, errors, rate limit messages)

---

### 💻 Features Implemented
- 🔎 Search repositories by keyword (e.g., `react`, `node express`)
- ⚙️ Sort results by **Best match**, **Stars**, **Forks**, or **Recently updated**
- 📄 Paginate results using “Load more” (10 items per page)
- ⭐ Display repository info: **name**, **description**, **stars**, **forks**, **language**, **license**, and **Open on GitHub** link
- 🚫 Handle rate limit errors and display ETA until next allowed request
- 💎 Responsive 1–2 column layout for repository cards

---

### 🧠 Code Flow Explanation

#### 1️⃣ When the page loads
- The app defines a global `state` object containing `{ q, sort, page, isLoading, total }`.
- The input, select box, and buttons are wired with event handlers:
  - **Enter key** or **Search button** → `runSearch(true)`
  - **Load more** button → `runSearch(false)`
- The main UI elements are hidden or disabled until data is fetched.

#### 2️⃣ When user performs a search
- `runSearch(true)` is triggered:
  1. Reads keyword from `#q` and sort option from `#sort`.
  2. Validates input — if empty → `showError('Please enter a keyword.')`.
  3. Clears previous errors and results.
  4. Calls `setLoading(true)` → shows “Loading...” and disables button.
  5. Executes `searchGithub(q, sort, page)`:
     - Builds URLSearchParams.
     - Fetches from `https://api.github.com/search/repositories` with header `Accept: application/vnd.github+json`.
     - Parses response JSON.
     - Handles 403 errors (rate limit) using headers `x-ratelimit-remaining` and `x-ratelimit-reset`.
  6. Displays the first 10 repositories via `repoCard()`.
  7. If results remain → shows **Load more** button.
  8. Increments page counter.
  9. Calls `setLoading(false)`.

#### 3️⃣ When clicking “Load more”
- Calls `runSearch(false)` → repeats the same keyword and sort from state.
- Appends the next 10 results to the grid without clearing previous ones.
- Hides the button if fewer than 10 results are returned.

#### 4️⃣ Helper Functions
- `repoCard(r)` → builds HTML card for each repository with title, desc, stars, forks, language, and license.  
- `setLoading(v)` → toggles button state and loading spinner.  
- `showError(msg)` / `showNote(msg)` → display error or summary notes.  
- `clearResults()` → empties the results grid when resetting a search.

---

### 🧭 Complete User Flow Summary

```
[Page Load]
 └─ state initialized
 └─ UI ready (input, sort, search button)

[User enters keyword + clicks Search]
 └─ runSearch(true)
      ↳ validate input
      ↳ setLoading(true)
      ↳ searchGithub()
           ↳ fetch from API
           ↳ parse results / handle rate limit
      ↳ render repoCard() list
      ↳ toggle Load more
      ↳ setLoading(false)

[User clicks Load more]
 └─ runSearch(false)
      ↳ append next page of results
      ↳ hide button if no more
```

---

### 🧩 Edge Cases
- ❌ Empty keyword → “Please enter a keyword.”  
- ❌ No results → “No repositories found. Try another keyword.”  
- ⚠️ Rate limit reached (403) → shows ETA in minutes.  
- ⚠️ Network error or bad status → “Failed to fetch from GitHub.”  
- ⚙️ Load more disabled if fewer than 10 items returned.

---

### ✅ Example Scenario
1. User opens the app → input box and sort dropdown appear.  
2. Types **react** → clicks **Search**.  
3. “Loading...” appears briefly, then 10 repositories show up.  
4. Clicks **Load more** → next 10 repos appear appended below.  
5. Changes sort to **Stars** → results reloaded in descending star order.  
6. Quickly spams search → hits rate limit → friendly message “GitHub rate limit reached (~1 min)”.  

---

### 🧾 API Reference and Testing

**Endpoint:**  
```
GET https://api.github.com/search/repositories?q=<keyword>&sort=<stars|forks|updated>&order=desc&per_page=10&page=<n>
```

**Headers:**  
```
Accept: application/vnd.github+json
```

**Testing Checklist:**  
- ✅ Search with `react` → results appear  
- ✅ Sort by `stars` → highest first  
- ✅ Click “Load more” → appends next results  
- ❌ Empty input → shows warning  
- ⚠️ Spam requests → rate limit handled gracefully  

---

### 📁 File Structure
- **App:** `task2-2-github-finder.html`  
- All HTML, CSS, and JS logic in one file  
- Requires Live Server or local host (no API token needed)

---

### 📚 Key Takeaways
- Practical usage of **GitHub REST API** and `fetch()` error handling.  
- Shows how to manage **pagination and dynamic rendering**.  
- Demonstrates **async/await**, **state-driven UI**, and **user feedback flow**.  
- Teaches graceful handling of **rate limits** and real-time user experience control.  
- Strengthens understanding of front-end API-driven applications.



**Author:** [Hoang Xuan Dung]  
**Course:** Web application Development Laboratory 

