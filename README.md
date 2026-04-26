# 🛍️ ZENYTH — Complete Project Documentation

## 🏗️ Project Structure

```
zenyth/
├── index.html              ← Main SPA shell (navbar, footer, modals)
├── css/
│   ├── animations.css      ← All keyframes & motion utilities
│   ├── components.css      ← Reusable UI: buttons, cards, forms, toasts
│   └── style.css           ← Design tokens, layout, all page styles
├── js/
│   ├── data.js             ← Product database (30+ products) + helpers
│   ├── cart.js             ← Cart & wishlist with localStorage
│   ├── search.js           ← Live search overlay with suggestions
│   ├── ui.js               ← Toast, theme, scroll, countdown, preloader
│   └── app.js              ← SPA router + all page renderers
├── backend/
│   └── server.cpp          ← Full C++ HTTP server + REST API
└── README.md               ← This file
```

---

## 🎨 Design System

| Token | Value |
|---|---|
| Primary BG | `#0B0B14` (Deep Space) |
| Surface | `#13131F` |
| Violet | `#7C3AED` |
| Violet Light | `#A78BFA` |
| Coral | `#FF6347` |
| Display Font | Syne (800 weight) |
| Body Font | DM Sans |
| Mono Font | DM Mono |

---

## 📄 Pages & Features

| Page | Features |
|---|---|
| **Home** | Hero + live search, category grid, promo banners, featured products, flash sale countdown, new arrivals, trust strip |
| **Products** | Filter sidebar, sort bar, view toggle (grid/list), category radio, price range slider, star filter, brand filter |
| **Product Detail** | Image gallery, variants, qty selector, add to cart, buy now, wishlist, tabs (description/reviews/specs), related products |
| **Cart** | Dynamic qty update, remove items, coupon code system, order summary, proceed to checkout |
| **Checkout** | Multi-step UI, shipping form, payment methods (UPI/Card/NetBanking/COD), order success state |
| **Login/Signup** | Social login buttons, form validation, tab switching, password toggle |

---

## 🚀 Deployment — GitHub Pages (FREE)

### Step 1 — Create GitHub Account
Visit [github.com](https://github.com) → Sign Up (free)

### Step 2 — Create Repository
1. Click **+** → **New repository**
2. Name: `zenyth` (or your brand name)
3. Set to **Public**
4. Click **Create repository**

### Step 3 — Push Your Files
```bash
# Inside your /zenyth folder
git init
git add index.html css/ js/
git commit -m "feat: launch ZENYTH marketplace"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/zenyth.git
git push -u origin main
```

### Step 4 — Enable GitHub Pages
1. Repository → **Settings** → **Pages**
2. Source: **Deploy from branch → main → / (root)**
3. Click **Save** — wait ~2 minutes

### Step 5 — Your Store is Live! 🎉
```
https://YOUR_USERNAME.github.io/zenyth/
```

---

## 🌐 Deployment — Netlify (Drag & Drop — 30 seconds)

1. Go to **[netlify.com](https://netlify.com)** → Sign up free
2. Click **"Add new site"** → **"Deploy manually"**
3. **Drag your `/zenyth` folder** (without `backend/`) onto the page
4. Done! URL: `https://your-site-name.netlify.app`

### Custom Domain
1. **Site settings** → **Domain management** → **Add custom domain**
2. Free SSL auto-configured ✅

---

## 🌐 Deployment — Vercel (Fastest CDN)

```bash
npm install -g vercel
cd zenyth
vercel --prod
# → https://zenyth.vercel.app
```

---

## ⚙️ C++ Backend — Local Setup

```bash
cd zenyth/backend

# Compile (Linux/macOS)
g++ -std=c++17 -O2 -pthread -o server server.cpp

# Run
./server
# → http://localhost:8080

# Test APIs
curl "http://localhost:8080/api/health"
curl "http://localhost:8080/api/products?category=electronics&sort=rating"
curl "http://localhost:8080/api/products/e001"
curl "http://localhost:8080/api/products?q=laptop"
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Priya","email":"priya@test.com","password":"pass1234"}'
```

### Connect Frontend to Backend
In `js/app.js`, inside `_initCheckoutEvents()`, replace the `setTimeout` mock with:
```javascript
const res = await fetch('http://localhost:8080/api/orders', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${localStorage.getItem('zenyth_token')}`
  },
  body: JSON.stringify({
    items: Cart.getItems(),
    address: { city, pin, address },
    payment_method: selectedMethod
  })
});
const data = await res.json();
```

---

## 🏭 Production Backend Stack

### Nginx Reverse Proxy Config
```nginx
server {
    listen 443 ssl http2;
    server_name api.zenyth.in;

    ssl_certificate     /etc/letsencrypt/live/api.zenyth.in/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.zenyth.in/privkey.pem;

    # Serve static assets directly
    location /css/ { root /var/www/zenyth; expires 1d; }
    location /js/  { root /var/www/zenyth; expires 1d; }

    # Proxy API calls to C++ server
    location /api/ {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # SPA — serve index.html for all routes
    location / {
        root /var/www/zenyth;
        try_files $uri /index.html;
    }
}
```

### Docker Deployment
```dockerfile
# Dockerfile
FROM gcc:12 AS builder
WORKDIR /build
COPY backend/server.cpp .
RUN g++ -std=c++17 -O2 -pthread -o server server.cpp

FROM ubuntu:22.04
WORKDIR /app
COPY --from=builder /build/server .
COPY index.html .
COPY css/ css/
COPY js/ js/
EXPOSE 8080
CMD ["./server"]
```

```bash
docker build -t zenyth-server .
docker run -d -p 8080:8080 --name zenyth zenyth-server
```

---

## 📈 Scaling Into a Real Startup

### Phase 1 — MVP (Month 1–2) ₹0–₹5K/mo
- Deploy frontend on Netlify/Vercel (free)
- Backend: Railway.app or Render.com (free tier)
- Database: Supabase (free PostgreSQL)
- Payments: Razorpay (free to integrate, 2% per txn)
- Email: Resend.com (3,000 emails/month free)

### Phase 2 — Growth (Month 3–6) ₹5K–₹30K/mo
- Move to dedicated VPS (DigitalOcean $12/mo)
- Add Redis for caching & sessions
- Integrate Elasticsearch for product search
- Add Cloudflare CDN for images
- Build seller onboarding portal
- Mobile app (React Native or Flutter)

### Phase 3 — Scale (Month 7–12) ₹30K+/mo
- Move to Kubernetes on AWS EKS / GCP GKE
- Separate microservices (Product, Order, User, Search)
- Add Kafka for async event processing
- Add ML-powered recommendations
- Add A/B testing framework
- Multiple payment gateways (Razorpay + PayU + BNPL)

### Phase 4 — Enterprise (Year 2+)
- Multi-region deployment (Mumbai + Singapore + US)
- Own logistics network or Dunzo/Shiprocket integration
- Seller analytics dashboard
- Ad platform for sellers (promoted listings)
- Loyalty program & ZENYTH Coins

---

## 💰 Monetization Ideas

| Strategy | Revenue Model | Potential (Monthly) |
|---|---|---|
| **Commission** | 5–20% cut on each sale | ₹50K–₹10L+ |
| **Seller Subscription** | ₹999–₹4,999/mo for premium seller tools | ₹25K–₹5L |
| **Promoted Listings** | Sellers pay for top placement (like Amazon Ads) | ₹20K–₹2L |
| **ZENYTH Plus** | ₹199/mo subscription for free delivery + early deals | ₹15K–₹5L |
| **B2B Wholesale** | Bulk ordering portal for businesses | ₹1L–₹20L |
| **White-Label** | License this platform to other businesses | ₹2L–₹50L one-time |
| **Data Insights** | Anonymized trend reports to brands | ₹50K–₹2L |

---

## ✅ Pre-Launch Checklist

- [ ] Replace placeholder content and product images
- [ ] Set up Razorpay account for real payments
- [ ] Connect contact form to backend (Formspree or own API)
- [ ] Add Google Analytics 4 (`gtag.js` in `<head>`)
- [ ] Submit sitemap to Google Search Console
- [ ] Set up Google Shopping feed (product XML)
- [ ] Test on Chrome, Safari, Firefox, iOS Safari, Android Chrome
- [ ] Run Lighthouse audit — aim 90+ Performance, 100 Accessibility
- [ ] Add favicon + Apple Touch Icon
- [ ] Set up WhatsApp Business for customer support
- [ ] Register business (LLP or Pvt. Ltd. — ₹8K–₹15K via Indiafilings.com)
- [ ] Open business bank account (Razorpay Current Account — free)
- [ ] Get GST registration (mandatory if turnover > ₹40L)

---

## 🏆 Tech Stack Summary

| Layer | Technology |
|---|---|
| Frontend Structure | HTML5 (semantic, ARIA accessible) |
| Styling | CSS3 (custom properties, grid, animations) |
| Interactivity | Vanilla JS ES6+ (SPA router, DOM rendering) |
| State Management | localStorage (cart, theme, wishlist) |
| Backend | C++17 (POSIX sockets, REST API) |
| Display Font | Syne (Google Fonts) |
| Body Font | DM Sans (Google Fonts) |
| Icons | Font Awesome 6 |
| Payment (suggested) | Razorpay |
| Deploy | GitHub Pages / Netlify / Vercel |
| DB (suggested) | PostgreSQL + Redis |
| Search (suggested) | Elasticsearch |

---

*ZENYTH — Discover. Buy. Elevate.*
*Built with ❤️ · Production-ready architecture · 2024*
