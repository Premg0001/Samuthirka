# Samthurika Gold Jewellery Private Limited — E-Commerce Website

A premium, mobile-first gold jewellery catalog & enquiry site built with Next.js 14 (App Router),
React, TypeScript, Tailwind CSS, and Firebase (Auth, Firestore, Storage). Checkout happens over
WhatsApp instead of a payment gateway.

## ✨ Features

- Luxury gold/black/white themed UI with smooth animations
- Home page: hero banner, categories, featured jewellery, new arrivals, reviews
- 10 product categories (chains, necklaces, bangles, rings, earrings, bracelets, pendants, kids,
  men's, coins)
- Product cards & detail pages with **live, auto-calculated pricing**:
  `Total = (Weight × Current CMP) + Making Charge + GST`
- Cart with quantity controls, GST breakdown, grand total
- **Checkout on WhatsApp** — no payment gateway, opens WhatsApp with a pre-filled enquiry message
  to **+91 78109 41027**
- Instant search (name / model number / category) + filters (weight, price, category, purity)
- Admin Panel (auth-protected): Product CRUD with multi-image upload to Firebase Storage, CMP
  Management (update gold rate once → every product price recalculates automatically), dashboard
  stats, enquiries log
- Firestore security rules so only verified admins can write data
- SEO metadata, sitemap.xml, robots.txt, lazy-loaded images, loading skeletons, toast
  notifications, dark mode toggle, error boundaries

## 🧱 Tech Stack

Next.js 14 · React 18 · TypeScript · Tailwind CSS · Firebase (Auth, Firestore, Storage) · Vercel

## 📁 Project Structure

```
src/
  app/                     # Next.js App Router pages
    page.tsx               # Home
    products/               # Listing + [id] detail page
    cart/                   # Cart + WhatsApp checkout
    about/, contact/        # Static info pages
    admin/                  # Auth-protected admin panel
      login/ dashboard/ products/ cmp/ enquiries/
  components/              # Reusable UI components
  context/                 # CartContext, AuthContext (React Context)
  hooks/                   # useProducts, useCmpSettings
  lib/                     # firebase.ts, types.ts, pricing.ts, categories.ts
firestore.rules            # Firestore security rules
storage.rules               # Storage security rules
scripts/seed.md             # How to create your first admin + CMP doc
```

## 🔥 Firebase Setup

1. Go to [console.firebase.google.com](https://console.firebase.google.com) → **Create a project**.
2. **Build → Authentication** → Sign-in method → enable **Email/Password**.
3. **Build → Firestore Database** → Create database (production mode).
4. **Build → Storage** → Get started (default bucket).
5. Project settings → General → "Your apps" → Add a **Web app** → copy the config values into
   `.env.local` (see below).
6. Deploy security rules (or paste them manually in the Firebase Console → Rules tab):
   ```bash
   npm install -g firebase-tools
   firebase login
   firebase init firestore storage   # point to this project, use the existing firestore.rules / storage.rules
   firebase deploy --only firestore:rules,storage:rules
   ```
7. Follow `scripts/seed.md` to create your first **admin** user and the initial **CMP** settings
   document.

## ⚙️ Environment Variables

Copy `.env.local.example` to `.env.local` and fill in your Firebase web config:

```bash
cp .env.local.example .env.local
```

```
NEXT_PUBLIC_FIREBASE_API_KEY=...
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=...
NEXT_PUBLIC_FIREBASE_PROJECT_ID=...
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=...
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=...
NEXT_PUBLIC_FIREBASE_APP_ID=...
NEXT_PUBLIC_WHATSAPP_NUMBER=917810941027
NEXT_PUBLIC_CALL_NUMBER=+917810941027
```

## 💻 Run Locally

```bash
npm install
npm run dev
```

Visit `http://localhost:3000`. Admin panel is at `http://localhost:3000/admin/login`.

## 🚀 Deploy on Vercel

1. Push this project to a GitHub repository.
2. Go to [vercel.com/new](https://vercel.com/new) → Import the repo.
3. In **Environment Variables**, add every variable from `.env.local`.
4. Deploy. Vercel auto-detects Next.js — no extra config needed.
5. Add your Vercel domain to Firebase Console → Authentication → Settings → **Authorized domains**.

## 🧮 How Pricing Works

All pricing logic lives in **`src/lib/pricing.ts`**:

```
Gold Value = Weight (g) × CMP for that purity (₹/g)
Subtotal   = Gold Value + Making Charge
GST        = Subtotal × GST%
Total      = Subtotal + GST
```

Admins never edit a product's price directly. They update the day's gold rate once in
**Admin → CMP Management** (separate rates for 22K and 18K, plus GST%), and every product's price
across the entire site recalculates instantly because prices are computed at render time, not
stored.

## 📦 Firestore Collections

| Collection  | Purpose                                                          |
|-------------|-------------------------------------------------------------------|
| `products`  | Jewellery catalog items                                           |
| `categories`| (Optional) category metadata — a static list also exists in code  |
| `settings`  | Single `cmp` document holding gold rates & GST%                   |
| `orders`    | Cart/contact enquiries submitted by customers                     |
| `admin`     | UID → admin profile; presence of a doc = admin access granted     |

## 🔒 Security

- Firestore & Storage rules (`firestore.rules`, `storage.rules`) restrict all writes to verified
  admins; product/category/settings reads are public so the storefront works for everyone.
- The `admin` collection is **never client-writable** — add admins only via the Firebase Console
  or Admin SDK, by design, to prevent privilege escalation from the browser.
- The Admin Panel checks both Firebase Auth session **and** a matching `admin/{uid}` Firestore doc
  before rendering any dashboard route (`src/app/admin/layout.tsx`).

## 🔮 Future-Ready Architecture

The data model and folder structure are intentionally left extensible for:

- Online Payments (swap the WhatsApp checkout button for a payment gateway button in `cart/page.tsx`)
- Customer Login / Wishlist (reuse `AuthContext`, add a `users` and `wishlists` collection)
- Order Tracking / Invoice PDF (extend the `orders` collection with a `status` timeline + a PDF
  export route)
- Barcode Search / QR Code Sharing (model number already indexed — add a barcode scanner lib and
  a `/products/[id]` QR generator)
- Multi-Branch Support / Multiple Admins (the `admin` collection already supports a `role` field;
  add a `branch` field to `products` and `admin`)
- Offer Banners / Discount Coupons / Analytics (add `banners` and `coupons` collections; hook into
  the existing `settings` pattern used for CMP)

## 📄 License

Proprietary — built for Samthurika Gold Jewellery Private Limited.
