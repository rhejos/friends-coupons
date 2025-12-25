# 🧠 How Friend Coupons Works

A beginner-friendly guide to understanding the code structure and logic behind this app. Perfect for learning web development!

---

## 📁 File Structure

This is a **single-file application** - everything lives in one `index.html` file:

```
index.html
├── <head>           → Page title, settings
├── <style>          → All the CSS (how it looks)
├── <body>           → The HTML structure (what you see)
└── <script>         → The JavaScript (how it works)
```

**Why one file?** Makes it easy to deploy anywhere (like GitHub Pages) without build tools or complex setups.

---

## 🏗️ The Three Layers

### 1. HTML (Structure) - "The Skeleton"
```
What it does: Defines WHAT appears on the page
```

Think of HTML like the frame of a house. It creates:
- Tabs (Create, My Coupons, Notifications, Settings)
- Forms (input fields, buttons, dropdowns)
- Containers (where coupons will be displayed)
- Modals (popup windows for sharing)

**Key sections:**
```html
<div id="appView">        <!-- Main app for admin -->
<div id="redeemView">     <!-- What friends see when redeeming -->
<div id="shareModal">     <!-- WhatsApp sharing popup -->
<div id="printArea">      <!-- Hidden area for printing -->
```

### 2. CSS (Styling) - "The Paint & Decoration"
```
What it does: Defines HOW things look
```

The CSS creates:
- Dark theme (gray-900 background)
- Purple gradient accents
- Rounded corners and shadows
- Responsive design (works on phones)
- Print styles (hides buttons when printing)

**Key patterns:**
```css
.card { }           /* White-ish boxes that hold content */
.btn { }            /* Button styling */
.coupon-card { }    /* Individual coupon display */
@media print { }    /* Special rules for printing */
```

### 3. JavaScript (Logic) - "The Brain"
```
What it does: Defines HOW things behave
```

The JavaScript handles:
- Creating and saving coupons
- Generating QR codes and share links
- Checking if someone can redeem
- Sending webhook notifications
- All the interactive parts

---

## 💾 Data Storage Explained

### Where Data Lives

```
┌─────────────────────────────────────────────────────┐
│                    localStorage                      │
│  (Browser's built-in storage - like a mini database)│
├─────────────────────────────────────────────────────┤
│  friendCoupons     → Array of all your coupons      │
│  friendNotifications → Array of redemption logs     │
│  friendWebhook     → Your IFTTT webhook URL         │
│  myRedemptions     → Coupons YOU have redeemed      │
└─────────────────────────────────────────────────────┘
```

### Coupon Data Structure

Each coupon is an **object** (a bundle of related data):

```javascript
{
    id: "1703520000000",        // Unique identifier (timestamp)
    code: "ABC123",             // 6-letter code for sharing
    title: "Free Coffee",       // What you named it
    description: "...",         // Details about the coupon
    value: 5.00,                // Dollar amount
    color: "#a855f7",           // Border color (hex code)

    // WHO can redeem
    redemptionType: "single",   // "single", "limited", or "unlimited"
    maxRedemptions: 1,          // How many different people

    // HOW OFTEN per person
    usesPerPeriod: 1,           // Uses allowed per period
    period: "month",            // "forever", "day", "week", "month", "year"

    redemptions: [              // Track who redeemed
        { name: "Alice", time: "2024-01-15T..." }
    ],
    createdAt: "2024-01-01T..." // When created
}
```

---

## 🔗 How Sharing Works

### The Problem
Your coupons are stored in YOUR browser. When a friend clicks your link, they don't have your data!

### The Solution: Encode Data in the URL

```
Step 1: Take coupon data
        ↓
Step 2: Convert to JSON string
        ↓
Step 3: Encode so it's URL-safe (btoa + encodeURIComponent)
        ↓
Step 4: Add to URL after #redeem/

Result: yoursite.com/#redeem/eyJjIjoiQUJDMTIzIi...
```

**In code:**
```javascript
function getRedeemUrl(coupon) {
    const couponData = {
        c: coupon.code,           // Short keys to save space
        t: coupon.title,
        v: coupon.value,
        // ... more fields
        wh: webhookUrl            // Include webhook so friends can notify you!
    };

    // Convert object → JSON string → base64 encoded
    const encoded = btoa(encodeURIComponent(JSON.stringify(couponData)));

    return `${window.location.origin}#redeem/${encoded}`;
}
```

**Why this works:**
- All coupon info travels WITH the link
- Friends don't need your localStorage
- QR codes encode this same URL
- Webhook URL included so notifications work from any device

---

## ⏰ How Frequency Limits Work

### The Logic Flow

```
When someone tries to redeem:
        ↓
┌─────────────────────────────┐
│ Get their past redemptions  │ ← From localStorage
│ for THIS specific coupon    │
└─────────────────────────────┘
        ↓
┌─────────────────────────────┐
│ What's the period?          │
│ • forever (one-time)        │
│ • day / week / month / year │
└─────────────────────────────┘
        ↓
┌─────────────────────────────┐
│ Calculate period start      │ ← When did current period begin?
│ • day: midnight today       │
│ • week: Monday              │
│ • month: 1st of month       │
│ • year: January 1st         │
└─────────────────────────────┘
        ↓
┌─────────────────────────────┐
│ Count redemptions since     │
│ period start                │
└─────────────────────────────┘
        ↓
┌─────────────────────────────┐
│ Compare to usesPerPeriod    │
│ If count < allowed → ✅ OK  │
│ If count >= allowed → ❌ NO │
└─────────────────────────────┘
```

**In code:**
```javascript
function canRedeemAgain(coupon, myRedemptions) {
    const period = coupon.period;
    const usesPerPeriod = coupon.usesPerPeriod;

    // Get only redemptions for THIS coupon
    const couponRedemptions = myRedemptions.filter(r => r.code === coupon.code);

    // If one-time only, just check if they've ever redeemed
    if (period === 'forever') {
        return couponRedemptions.length < usesPerPeriod;
    }

    // Otherwise, count only redemptions in CURRENT period
    const periodStart = getPeriodStart(period);
    const redemptionsThisPeriod = couponRedemptions.filter(r =>
        new Date(r.time).getTime() >= periodStart
    );

    return redemptionsThisPeriod.length < usesPerPeriod;
}
```

---

## 🔔 How Webhooks Work

### What's a Webhook?
A webhook is like giving someone your phone number and saying "text me when X happens."

```
┌──────────────┐         ┌──────────────┐         ┌──────────────┐
│  Your App    │ ──────► │    IFTTT     │ ──────► │  Your Phone  │
│              │  POST   │  (Webhook)   │  Push   │  Notification│
│  "Hey, Bob   │ request │              │  notif  │  "Bob just   │
│   redeemed!" │         │              │         │   redeemed!" │
└──────────────┘         └──────────────┘         └──────────────┘
```

### The Code
```javascript
// When someone redeems, send data to IFTTT
await fetch(webhookUrl, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        value1: name,           // Who redeemed
        value2: coupon.title,   // What coupon
        value3: coupon.value    // Dollar value
    })
});
```

### Why It's In The URL
The webhook URL is stored in YOUR browser. But when a friend redeems on THEIR phone, they don't have it!

**Solution:** Include the webhook URL in the shared link data, so when they redeem, the app knows where to send the notification.

---

## 🎨 How the UI Updates

### The Pattern: Render Functions

Instead of updating individual pieces, we RE-RENDER entire sections:

```javascript
function renderCoupons() {
    const container = document.getElementById('couponList');

    // Generate HTML for ALL coupons
    container.innerHTML = coupons.map(coupon => `
        <div class="coupon-card">
            <div class="coupon-title">${coupon.title}</div>
            <!-- ... more HTML ... -->
        </div>
    `).join('');
}
```

**Why this works:**
1. Simple to understand
2. Always shows current data
3. No complex state tracking

**When we call render functions:**
- After creating a coupon → `renderCoupons()`
- After deleting a coupon → `renderCoupons()`
- On page load → `renderCoupons()`, `renderNotifications()`

---

## 🖨️ How Printing Works

### The Trick: CSS Media Queries

```css
/* Normal styles */
.no-print {
    display: block;  /* Visible normally */
}

/* When printing */
@media print {
    .no-print {
        display: none !important;  /* Hidden when printing */
    }

    .print-area {
        display: block !important;  /* Show print content */
    }
}
```

### The Flow
```
1. User clicks "Print" button
        ↓
2. JavaScript creates printable HTML in #printArea
        ↓
3. Call window.print()
        ↓
4. Browser applies @media print styles
        ↓
5. Only print-friendly content shows
        ↓
6. User prints physical coupon!
```

---

## 🔐 How Admin Protection Works

### The Simple Approach

There's no login system. Instead:

```
┌─────────────────────────────────────────────────────┐
│ If URL has #redeem/...                              │
│   → Show ONLY the redemption view                   │
│   → Hide all admin tabs                             │
│   → No way to access coupon management              │
│                                                      │
│ If URL is just the base site                        │
│   → Show full admin interface                       │
│   → Can create, edit, delete coupons                │
└─────────────────────────────────────────────────────┘
```

**In code:**
```javascript
function checkForRedeemUrl() {
    if (window.location.hash.startsWith('#redeem/')) {
        // Hide admin, show only redeem view
        document.getElementById('appView').style.display = 'none';
        document.getElementById('redeemView').classList.add('active');
    }
}
```

**Why this is enough:**
- Friends only get redemption links
- They never see the base URL
- Even if they remove the hash, they don't have YOUR localStorage data

---

## 📱 QR Code Generation

### Using an External API

We don't generate QR codes ourselves - we use a free API:

```javascript
function getQRUrl(coupon) {
    const redeemUrl = getRedeemUrl(coupon);

    // Ask api.qrserver.com to create a QR code image
    return `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${encodeURIComponent(redeemUrl)}`;
}
```

Then in HTML:
```html
<img src="${getQRUrl(coupon)}" alt="QR Code">
```

**How it works:**
1. We construct a URL with our data
2. The API returns an image
3. Browser displays the image
4. Scanning the QR → opens our redemption URL

---

## 🧩 Key JavaScript Concepts Used

### 1. Template Literals (Backticks)
```javascript
// Old way
"Hello " + name + ", you have " + count + " coupons"

// Template literal way
`Hello ${name}, you have ${count} coupons`
```

### 2. Array Methods
```javascript
// .map() - Transform each item
coupons.map(c => c.title)  // Get all titles

// .filter() - Keep only matching items
coupons.filter(c => c.value > 0)  // Only coupons with value

// .find() - Get first matching item
coupons.find(c => c.code === 'ABC123')  // Find by code

// .join() - Combine array into string
['a', 'b', 'c'].join('')  // "abc"
```

### 3. localStorage
```javascript
// Save data (must be a string)
localStorage.setItem('key', JSON.stringify(myObject));

// Load data (parse back to object)
const data = JSON.parse(localStorage.getItem('key') || '[]');

// Delete data
localStorage.removeItem('key');
```

### 4. async/await
```javascript
// Waiting for network requests
async function redeemCoupon() {
    // 'await' pauses until fetch completes
    await fetch(webhookUrl, { method: 'POST', ... });

    // This runs AFTER the request finishes
    showToast('Done!');
}
```

---

## 🎯 Design Decisions Explained

### Why localStorage instead of a database?
- **Simplicity**: No server needed
- **Privacy**: Data stays on user's device
- **Free**: No hosting costs
- **Trade-off**: Data doesn't sync between devices

### Why encode data in URLs?
- **Sharing**: Recipients get all info they need
- **Offline**: Works even if creator's site is down
- **Stateless**: No session management needed

### Why single HTML file?
- **Deployment**: Just upload one file
- **No build tools**: Works directly in browser
- **Beginner-friendly**: Easy to understand and modify

### Why IFTTT for notifications?
- **Free tier**: No cost for personal use
- **Flexible**: Can connect to many services
- **No backend**: Our app stays serverless

---

## 🛠️ How to Modify the Code

### Adding a New Coupon Field

1. **Add to the form** (HTML section):
```html
<div class="form-group">
    <label>🆕 My New Field</label>
    <input type="text" id="myNewField">
</div>
```

2. **Include when creating** (in `createCoupon()`):
```javascript
const coupon = {
    // ... existing fields ...
    myNewField: document.getElementById('myNewField').value,
};
```

3. **Add to URL encoding** (in `getRedeemUrl()`):
```javascript
const couponData = {
    // ... existing fields ...
    mnf: coupon.myNewField,  // Short key
};
```

4. **Parse from URL** (in `parseCouponFromUrl()`):
```javascript
return {
    // ... existing fields ...
    myNewField: data.mnf || '',
};
```

5. **Display it** (in `renderCoupons()` or `showRedeemView()`):
```javascript
<div>${coupon.myNewField}</div>
```

---

## 📚 Learning Resources

Want to learn more? Here are the concepts used:

- **HTML/CSS Basics**: [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Learn)
- **JavaScript**: [javascript.info](https://javascript.info/)
- **localStorage**: [MDN localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
- **Fetch API**: [MDN Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- **Base64 Encoding**: [MDN btoa](https://developer.mozilla.org/en-US/docs/Web/API/btoa)

---

Made with 💜 to help you learn!
