# 🎟️ Friend Coupons

A cute, single-file web app for creating and sharing custom coupons with friends! Perfect for gift-giving, IOUs, or just spreading joy.

## 🚀 Deploy to GitHub Pages

1. **Go to your repository** on GitHub
2. Click **Settings** (top menu)
3. Scroll down to **Pages** (left sidebar)
4. Under "Source", select your branch (e.g., `main`)
5. Click **Save**
6. Wait 1-2 minutes, then your app will be live at:
   ```
   https://YOUR-USERNAME.github.io/friends-coupons/
   ```

## 📱 How to Use

### Creating Coupons (Admin)

1. **Open your deployed site** in a browser
2. You're on the **✨ Create** tab by default
3. **Quick start**: Click a template button like "☕ Coffee & Chaos" to auto-fill
4. **Or customize**:
   - 🏷️ **Title**: Name your coupon (e.g., "Free Hug")
   - 📝 **Description**: What the coupon is good for
   - 💰 **Value**: Dollar amount (or $0 for priceless things)
   - 🎨 **Color**: Pick a color for the coupon border
   - 🔢 **Redemption Type**: Control how many people can redeem
5. Click **🎟️ Create Coupon**

### Who Can Redeem

| Type | Description |
|------|-------------|
| 👤 **Single Person** | Only 1 person can use this coupon |
| 👥 **Limited Group** | Set max number of people (e.g., first 5 friends) |
| 🌍 **Unlimited** | Anyone can use the coupon |

### How Often Per Person

| Frequency | Description |
|-----------|-------------|
| 1️⃣ **One Time Only** | Each person can only redeem once ever |
| 📅 **Resets Daily** | Can redeem once per day |
| 📆 **Resets Weekly** | Can redeem once per week |
| 🗓️ **Resets Monthly** | Can redeem once per month (great for subscriptions!) |
| 📅 **Resets Yearly** | Can redeem once per year |
| ⚙️ **Custom** | Set custom like "2 times per month" |

**Example Use Cases:**
- 🍦 Monthly ice cream coupon: Single person + Resets Monthly
- ☕ Weekly coffee date: Limited group (5) + Resets Weekly
- 🎁 Birthday coupon: Anyone + One Time Only

### Sharing Coupons

Go to the **🎫 My Coupons** tab to see all your coupons. For each coupon you can:

| Button | What it does |
|--------|--------------|
| 📋 **Copy Link** | Copies the redemption URL to clipboard |
| 💬 **WhatsApp** | Opens WhatsApp with a cute pre-written message |
| 📥 **Download** | Saves the coupon as a PNG image |
| 🖨️ **Print** | Opens print dialog for physical coupons |
| 🗑️ **Delete** | Remove the coupon |

### WhatsApp Message Styles

When sharing via WhatsApp, pick your vibe:
- 👋 **Group Chat Drop** - Perfect for friend groups
- 💕 **Sweet & Supportive** - Wholesome energy
- 😎 **Casual & Cute** - Chill vibes
- 🌪️ **Chaotic Bestie Energy** - Maximum chaos
- 📋 **Simple & Clean** - Just the essentials

### How Redemption Works

When someone receives your coupon link:
1. They click the link or scan the QR code
2. They see the coupon details (title, description, value)
3. They enter their name and click **🎉 Redeem**
4. You get notified via IFTTT webhook!

**Important**:
- Friends only see the redemption page - they can't access your admin area
- The QR code and links include all coupon data, so they work from any device
- Redemption limits are tracked in each person's browser
- For recurring coupons (daily/weekly/monthly), they can redeem again when the period resets

## 🔔 IFTTT Webhook Setup (Required for Notifications)

**Set this up BEFORE creating coupons** so notifications work when friends redeem!

1. Create an [IFTTT](https://ifttt.com) account
2. Create a new Applet:
   - **If**: Webhooks → "Receive a web request"
   - **Event name**: `coupon_redeemed` (or any name)
   - **Then**: Notifications → "Send a notification"
   - **Message**: `{{Value1}} redeemed "{{Value2}}" (${{Value3}})`
3. Go to [IFTTT Webhooks Settings](https://ifttt.com/maker_webhooks/settings) to get your key
4. Your webhook URL format: `https://maker.ifttt.com/trigger/YOUR_EVENT_NAME/with/key/YOUR_KEY`
5. Paste it in **⚙️ Settings** → Webhook URL
6. Click **💾 Save Webhook**

**How values are sent:**
- `Value1` = Redeemer's name
- `Value2` = Coupon title
- `Value3` = Dollar amount

## 🖨️ Printing Physical Coupons

1. Click **🖨️ Print** on any coupon
2. A clean, printable version appears with:
   - Decorative dashed border in the coupon's color
   - QR code for easy scanning
   - Large coupon code
   - Terms (single use, limited, or unlimited)
3. Print, cut out, and give to a friend!

## 💾 Data Storage

- **Your coupons** are stored in your browser's localStorage
- **Webhook URL** is embedded in shared links so friends can trigger notifications
- **Redemption tracking**: Each person's browser remembers what they redeemed

**Note**: If you clear your browser data, your coupons will be deleted. Friends can still redeem existing shared links.

## 🎨 Features

- ✨ Create custom coupons with titles, descriptions, values
- 🔢 Flexible redemption limits (single, limited, unlimited)
- 📱 QR codes that work from any device
- 💬 WhatsApp integration with 5 fun message templates
- 🖨️ Printable coupon designs
- 📥 Download as PNG images
- 🔔 IFTTT webhook notifications when someone redeems
- 🔒 Admin area hidden from redeemers
- 🌙 Dark theme with purple accents
- 📱 Mobile responsive

## 🛠️ Technical Details

- **Pure vanilla JavaScript** - No frameworks or build steps
- **Single HTML file** - Easy to deploy anywhere
- **localStorage** for persistence
- **QR codes** via [api.qrserver.com](https://goqr.me/api/)
- **Webhook** data embedded in shareable URLs

## ❓ Troubleshooting

**Not getting notifications?**
- Make sure you set up the webhook URL BEFORE creating coupons
- The webhook URL is baked into the shared link at creation time
- Try creating a new coupon after setting up the webhook

**QR code not working?**
- Make sure you're sharing the link from the app (not an old link)
- The QR encodes all coupon data so it works offline

**Friend can't redeem?**
- Check if the redemption limit was reached
- They may have already redeemed (check their browser)

---

Made with 💜 for friends everywhere!
