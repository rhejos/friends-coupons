# 🎟️ Friend Coupons

A cute, single-file web app for creating and sharing custom coupons with friends! Perfect for gift-giving, IOUs, or just spreading joy.

## 🚀 Deploy to GitHub Pages

1. **Go to your repository** on GitHub
2. Click **Settings** (top menu)
3. Scroll down to **Pages** (left sidebar)
4. Under "Source", select your branch (e.g., `main` or `claude/coupon-manager-app-lYfl8`)
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
   - 👥 **Group Coupon**: Check this if multiple friends can each redeem once
5. Click **🎟️ Create Coupon**

### Sharing Coupons

Go to the **🎫 My Coupons** tab to see all your coupons. For each coupon you can:

| Button | What it does |
|--------|--------------|
| 📋 **Copy Link** | Copies the redemption URL to clipboard |
| 💬 **WhatsApp** | Opens WhatsApp with a cute pre-written message |
| 📥 **Download** | Saves the coupon as a PNG image |
| 🖨️ **Print** | Opens print dialog for physical coupons |

### WhatsApp Message Styles

When sharing via WhatsApp, pick your vibe:
- 👋 **Group Chat Drop** - Perfect for friend groups
- 💕 **Sweet & Supportive** - Wholesome energy
- 😎 **Casual & Cute** - Chill vibes
- 🌪️ **Chaotic Bestie Energy** - Maximum chaos
- 📋 **Simple & Clean** - Just the essentials

### Redemption

When someone receives your coupon link:
1. They click the link (e.g., `yoursite.com/#redeem/ABC123`)
2. They see the coupon details
3. They enter their name and click **🎉 Redeem**
4. You'll see them in your notifications!

### Viewing Redemptions

- **🎫 My Coupons** tab shows who redeemed each coupon
- **🔔 Notifications** tab shows a log of all redemptions

## 🔔 IFTTT Webhook Setup (Optional)

Get push notifications when someone redeems a coupon:

1. Create an [IFTTT](https://ifttt.com) account
2. Create a new Applet:
   - **If**: Webhooks → "Receive a web request"
   - **Event name**: `coupon_redeemed`
   - **Then**: Notifications → "Send a notification"
   - **Message**: `{{Value1}} redeemed "{{Value2}}" (${{Value3}})`
3. Get your webhook URL from IFTTT (looks like `https://maker.ifttt.com/trigger/coupon_redeemed/with/key/YOUR_KEY`)
4. Paste it in **⚙️ Settings** → Webhook URL
5. Click **💾 Save Webhook**

## 🖨️ Printing Physical Coupons

1. Click **🖨️ Print** on any coupon
2. A clean, printable version appears with:
   - Decorative dashed border
   - QR code for easy scanning
   - Large coupon code
   - Terms (single use or group)
3. Print, cut out, and give to a friend!

## 💾 Data Storage

All data is stored in your browser's localStorage:
- Coupons persist between sessions
- Redemption history is saved
- **Note**: Data is per-browser/device. Clearing browser data will erase coupons.

## 🎨 Features

- ✨ Create custom coupons with titles, descriptions, values
- 👥 Group coupons (multiple people can redeem once each)
- 📱 QR codes for easy sharing
- 💬 WhatsApp integration with fun message templates
- 🖨️ Printable coupon designs
- 📥 Download as PNG images
- 🔔 In-app notifications + IFTTT webhook support
- 🌙 Dark theme with purple accents
- 📱 Mobile responsive

## 🛠️ Technical Details

- **Pure vanilla JavaScript** - No frameworks or build steps
- **Single HTML file** - Easy to deploy anywhere
- **localStorage** for persistence
- **QR codes** via [api.qrserver.com](https://goqr.me/api/)

---

Made with 💜 for friends everywhere!
