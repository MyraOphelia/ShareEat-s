<p align="center">
  <a href="https://shareeat-my.vercel.app/">
    <img src="ASSETS/LOGO.PNG" alt="ShareEat logo" width="120" />
  </a>
</p>

<h1 align="center">ShareEat</h1>

<p align="center">
  <strong>Full-stack surplus-food marketplace · Malaysia</strong><br />
  <em>Save food · Save money · Save Earth</em>
</p>

<p align="center">
  <a href="https://shareeat-my.vercel.app/"><img src="https://img.shields.io/badge/demo-live-4CAF50?style=flat-square" alt="Live demo" /></a>
  <img src="https://img.shields.io/badge/FYP-2026-0078D4?style=flat-square" alt="Final Year Project 2026" />
  <img src="https://img.shields.io/badge/roles-3_portals-purple?style=flat-square" alt="3 role portals" />
  <img src="https://img.shields.io/badge/i18n-4_languages-orange?style=flat-square" alt="4 languages" />
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-academic-lightgrey?style=flat-square" alt="License" /></a>
</p>

<p align="center">
  <a href="https://vercel.com"><img src="https://img.shields.io/badge/Vercel-hosting-000?style=flat-square&logo=vercel&logoColor=white" alt="Vercel" /></a>
  <a href="https://supabase.com"><img src="https://img.shields.io/badge/Supabase-backend-3FCF8E?style=flat-square&logo=supabase&logoColor=white" alt="Supabase" /></a>
  <img src="https://img.shields.io/badge/PostgreSQL-RLS-336791?style=flat-square&logo=postgresql&logoColor=white" alt="PostgreSQL RLS" />
  <img src="https://img.shields.io/badge/JavaScript-vanilla-F7DF1E?style=flat-square&logo=javascript&logoColor=black" alt="Vanilla JS" />
  <img src="https://img.shields.io/badge/PWA-enabled-5A0FC8?style=flat-square" alt="PWA" />
  <img src="https://img.shields.io/badge/CI-GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white" alt="GitHub Actions" />
</p>

<p align="center">
  <a href="#about">About</a> ·
  <a href="#highlights">Highlights</a> ·
  <a href="#live-demo">Live demo</a> ·
  <a href="#architecture">Architecture</a> ·
  <a href="#features">Features</a> ·
  <a href="#engineering">Engineering</a> ·
  <a href="#tech-stack">Tech stack</a> ·
  <a href="#documentation">Documentation</a> ·
  <a href="#author">Author</a>
</p>

---

## About

**ShareEat** is a production-style web platform that connects food businesses with customers to rescue edible surplus before it is discarded. Sellers list unsold items at reduced prices; buyers browse, order, and collect within a pickup window; administrators manage users, disputes, and platform operations.

Built as a **Final Year Project (2026)** in Software Engineering — designed, developed, and deployed end-to-end by a solo developer.

| | |
|---|---|
| **Problem** | Edible food is thrown away at closing time while people nearby want affordable meals. |
| **Solution** | One platform for listings, checkout, pickup, messaging, notifications, and admin support. |
| **Impact** | Less waste · lower prices · seller revenue recovery · tracked savings (meals, CO₂, RM) |
| **Market** | Malaysia — multilingual UI (EN / BM / ZH / TA), local maps and addresses |
| **SDGs** | UN SDG **12** (Responsible Consumption) · SDG **13** (Climate Action) |

> **Public documentation repository.** This repo showcases architecture, design decisions, and FYP materials. Application source code is **private** and not published here.

---

## Highlights

> *What makes this more than a CRUD app — built for real users, real roles, and real deployment.*

| | |
|---|---|
| **3 role-based portals** | Buyer, Seller, and Admin — each with dedicated workflows and access control |
| **Live production deploy** | Hosted on Vercel with Supabase backend — not localhost-only |
| **Security by design** | PostgreSQL Row Level Security (RLS), role-based auth, dual session model |
| **Multilingual product** | Full UI in English, Bahasa Malaysia, Chinese, and Tamil |
| **Real-world integrations** | Google Maps, Resend email, Supabase Edge Functions, PWA support |
| **Mobile-first buyer PWA** | Buyer app optimized for phone; seller & admin portals for desktop |
| **Documented end-to-end** | Architecture diagrams, schema docs, FYP requirements, 25+ markdown guides |

### By the numbers

| Metric | Count |
|--------|------:|
| Web pages (HTML) | 45+ |
| JavaScript modules | 70+ |
| SQL migrations & policies | 70+ |
| User roles | 3 |
| Supported languages | 4 |
| Documentation files | 25+ |

---

## Live demo

Try the deployed app — no install required.

| Portal | URL | Recommended device |
|--------|-----|--------------------|
| **Home** | [shareeat-my.vercel.app](https://shareeat-my.vercel.app/) | Any |
| **Buyer** | [shareeat-my.vercel.app/user](https://shareeat-my.vercel.app/user) | **Mobile** (PWA) |
| **Seller** | [shareeat-my.vercel.app/seller](https://shareeat-my.vercel.app/seller) | **Desktop** |
| **Admin** | [shareeat-my.vercel.app/admin](https://shareeat-my.vercel.app/admin) | **Desktop** |

**Support:** [support@shareeat.my](mailto:support@shareeat.my)

### User experience (Buyer)

ShareEat is a **PWA (Progressive Web App)** — optimized for mobile. **Buyers** should use ShareEat on their **phone** (add to home screen for app-like access). **Seller** and **Admin** portals are designed for **desktop** use.

<p align="center">
  <img src="ASSETS/SCREENSHOTS/USER-POSTER.PNG" alt="ShareEat buyer experience — mobile PWA for browsing and ordering surplus food" width="100%" />
</p>

<p align="center"><sub>Buyer journey — browse, order, and collect surplus food on mobile</sub></p>

---

## Architecture

ShareEat uses a **static frontend on Vercel** with **Supabase** as the backend (PostgreSQL, Auth, Storage, RLS, Edge Functions). The browser communicates directly with Supabase — no custom Node/API server to maintain.

<p align="center">
  <img src="ASSETS/SYSTEM-ARCHITECTURE.PNG" alt="ShareEat system architecture — users, Vercel frontend, Supabase backend, and external APIs" width="100%" />
</p>

<p align="center"><sub>Users & roles · Frontend (Vercel) · Backend (Supabase) · External APIs (Google Maps, Resend, AI Help Chat)</sub></p>

**Order flow:**

```mermaid
flowchart LR
    S[Seller lists surplus] --> B[Buyer browses & orders]
    B --> C[Checkout & pickup]
    S --> C
    C --> A[Admin support if needed]
```

Deep dive → **[DOCS/SYSTEM_ARCHITECTURE.md](DOCS/SYSTEM_ARCHITECTURE.md)** · **[DOCS/SYSTEM_DESIGN.md](DOCS/SYSTEM_DESIGN.md)** · **[DOCS/BACKEND.md](DOCS/BACKEND.md)**

---

## Features

### Buyer

- Browse home, map, and shop views with filters (category, time, price, free items)
- Bag, checkout, pickup tracking, and order history
- Profile — addresses, favourites, notifications, multi-language UI
- Order chat with seller, issue reporting, AI help chat
- Impact dashboard — meals saved, CO₂ avoided, money saved, badges

### Seller

- Dashboard with orders, revenue, and analytics
- Listing management — price, stock, images, pickup window, promotions
- Order lifecycle — confirm, prepare, ready, complete, reject
- Inventory and low-stock alerts
- Per-order customer messaging and store settings

### Admin

- User and seller management, global listings oversight
- Support desk and order disputes with buyer replies
- Payments, reports, preparation queue, audit logs
- Broadcast email, content management, localisation, feature flags

---

## Engineering

Technical decisions that matter to developers reviewing this project.

### Key design choices

| Decision | Rationale |
|----------|-----------|
| **Supabase BaaS** | Auth, Postgres, Storage, and RLS in one platform — ship faster without maintaining a custom API server |
| **Vanilla JavaScript** | No framework lock-in; direct control over performance, PWA behaviour, and page-level logic |
| **Dual auth clients** | Separate Supabase sessions for buyer vs seller/admin so one browser can hold both roles in different tabs |
| **RLS-first security** | Data access enforced at the database layer — not only in frontend code |
| **DB triggers & RPCs** | Stock sync, order lifecycle, and inventory consistency handled server-side |
| **Edge Functions** | Admin broadcast email and scheduled jobs without a dedicated backend host |

### Quality & reliability

- Automated tests for checkout math and i18n key parity
- GitHub Actions CI on push
- Pre-push secret scanning (`check-secrets`)
- Docker option for reproducible local runs
- Playwright E2E test suite (source repo)

### Skills demonstrated

`System Design` · `Full-Stack Development` · `PostgreSQL & RLS` · `REST / Supabase SDK` · `OAuth & Session Auth` · `Cloud Deployment (Vercel)` · `Third-Party API Integration` · `Responsive / Mobile UI` · `Internationalization (i18n)` · `Technical Documentation` · `CI/CD` · `Security Awareness`

---

## Tech stack

| Layer | Technologies |
|-------|----------------|
| **Frontend** | HTML5, CSS3, vanilla JavaScript, PWA, responsive mobile nav |
| **Portals** | Buyer · Seller · Admin (single codebase) |
| **Backend** | Supabase — PostgreSQL, Auth, Storage, RLS, Edge Functions |
| **Email** | Resend (`shareeat.my`) |
| **Maps** | Google Maps JavaScript API + Geocoding API |
| **Hosting** | Vercel (primary), Docker optional |
| **Tooling** | Node.js, Playwright, GitHub Actions |

| Integration | Purpose |
|-------------|---------|
| Supabase Auth | Email login, Google OAuth, role-based sessions |
| Supabase PostgREST | Orders, listings, chat, notifications, disputes |
| Supabase Storage | Listing images |
| Supabase Edge Functions | Admin broadcast, scheduled jobs |
| Google Maps / Geocoding | Map browse, shop pins, address geocoding |
| Resend | Transactional and admin email |

> Checkout UI includes FPX / card flows; live payment gateway is on the [roadmap](#roadmap).

---

## Documentation

```
ShareEat-s/
├── README.md          ← Project overview (you are here)
├── LICENSE
├── DOCS/              ← Architecture, design, backend, FYP
├── AI/                ← AI assistant module (report pack, diagram)
└── ASSETS/            ← Logo, diagrams, screenshots
```

| Topic | Document |
|-------|----------|
| System architecture | [DOCS/SYSTEM_ARCHITECTURE.md](DOCS/SYSTEM_ARCHITECTURE.md) |
| System design & decisions | [DOCS/SYSTEM_DESIGN.md](DOCS/SYSTEM_DESIGN.md) |
| Backend & data flow | [DOCS/BACKEND.md](DOCS/BACKEND.md) |
| Class / data model | [DOCS/CLASS-DIAGRAM.md](DOCS/CLASS-DIAGRAM.md) |
| Schema visualization | [DOCS/SCHEMA-VISUALIZATION.md](DOCS/SCHEMA-VISUALIZATION.md) |
| Business & revenue model | [DOCS/REVENUE_EXPLAINED.md](DOCS/REVENUE_EXPLAINED.md) |
| FYP requirements | [DOCS/FYP_FUNCTIONAL_REQUIREMENTS_TABLE.md](DOCS/FYP_FUNCTIONAL_REQUIREMENTS_TABLE.md) |
| FYP demo & report guide | [DOCS/FYP-RUNBOOK.md](DOCS/FYP-RUNBOOK.md) |
| AI assistant (FYP Ch. 4 & 6) | [AI/README.md](AI/README.md) |
| Full doc index | [DOCS/README.md](DOCS/README.md) |

---

## Roadmap

**Shipped (v1.0)**

- Three-role marketplace (buyer, seller, admin)
- Order disputes, admin replies, in-app notifications
- Multi-language UI (EN / BM / ZH / TA)
- Impact tracking, badges, AI help chat
- Mobile bottom navigation, automated tests, CI

**Planned**

- Live payment gateway (FPX / e-wallet)
- Push notifications and native mobile app
- AI API integration for chat
- Nationwide seller onboarding and ESG impact reporting

---

## Author

<table>
  <tr>
    <td width="120"><img src="ASSETS/LOGO.PNG" alt="ShareEat" width="80" /></td>
    <td>
      <strong>Myra Ophelia Iman Binti Maurice Feizal</strong><br />
      Bachelor of Software Engineering · Final Year Project · 2026<br /><br />
      Full-stack developer — designed, built, and deployed ShareEat end-to-end.<br /><br />
      <!-- Replace # with your profile URLs -->
      <a href="https://github.com/MyraOphelia">GitHub</a> ·
      <a href="https://www.linkedin.com/in/myra-ophelia-iman">LinkedIn</a> ·
      <a href="mailto:support@shareeat.my">Email</a> ·
      <a href="https://shareeat-my.vercel.app/">Live demo</a>
    </td>
  </tr>
</table>

**Interested in the project?** Star this repo, try the [live demo](https://shareeat-my.vercel.app/), or connect on LinkedIn — happy to walk through the architecture.

---

## License

Academic coursework — all rights reserved by **Myra Ophelia Iman Binti Maurice Feizal** unless stated by the institution. See **[LICENSE](LICENSE)**.
