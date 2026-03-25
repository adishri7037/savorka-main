# Savorka Solar - Comprehensive Documentation

## 1. Developer Documentation

### Project Overview and Purpose
**Savorka Solar** is a modern, responsive React frontend for a renewable energy company specializing in solar power solutions. The application serves dual purposes:
- **Public Marketing Site**: Promotes services (residential, commercial, housing society solar installations), showcases projects, handles lead generation via forms (contact, GoSolar, newsletters), and provides company information.
- **Admin Dashboard**: Secure panel for administrators to view/manage leads (categorized by Residential/Housing Society/Commercial), contact inquiries, newsletter subscribers, and comments. Real-time notifications for new entries.

The site drives user engagement for solar consultations/projects and provides backend admins with CRM-like tools for lead management.

### System Architecture
#### High-Level
```
[Users] <-> [React Frontend (Vite/Create React App)] <-> [Backend API (Node.js/Express assumed)] <-> [Database (MongoDB/PostgreSQL assumed)]
                          |
                          v
                   [Static Assets (Images, Fonts)]
```
- **Frontend**: Single-Page Application (SPA) with client-side routing.
- **Backend**: RESTful API (localhost:5000/api) handling auth, CRUD for leads/contacts/newsletters/comments.
- **Deployment**: Frontend static hosting (Vercel/Netlify); Backend separate (Heroku/Render/VPS).

#### Detailed Components
- **Routing**: React Router v6 with nested routes. Public: `/` (Home), `/about`, `/services`, `/projects`, `/contact`, etc. Admin: `/admin` (login), `/dashboard` (protected).
- **State Management**: Context API for auth (`AuthContext`). Local state for UI (e.g., modals, forms).
- **API Integration**: Axios implied (imported in deps). Fetch used in admin. Centralized base URL in `src/config/api.js`.
- **Animations**: Framer Motion for page transitions, hover effects, preloader.
- **Styling**: Tailwind CSS utility-first + custom theme (greens/navy palette).

**Data Flow Example (New Lead)**:
1. User submits form on frontend → POST to `/leads`.
2. Backend saves to DB, notifies admin.
3. Admin dashboard polls `/leads` every 10s, shows toast + notification badge.

### Tech Stack and Dependencies
| Category | Technologies |
|----------|--------------|
| Framework | React 18.2 (CRA), React Router 6.30 |
| Styling | Tailwind CSS 3.4, PostCSS, Autoprefixer |
| Animations | Framer Motion 12.38 |
| Icons | Lucide React 0.577, React Icons 5.6 |
| Notifications | React Hot Toast 2.6 |
| Utils | Axios 1.13, jsPDF 4.2 (PDF export?), Motion |
| Dev | React Scripts 5.0 |

**package.json scripts**: `npm start` (dev server), `npm run build` (production build).

### Installation and Setup (Step-by-Step)
1. **Prerequisites**: Node.js >=16, npm/yarn.
2. **Clone/Navigate**:
   ```
   cd Savorka_frontend
   ```
3. **Install Dependencies**:
   ```
   npm install
   ```
4. **Backend Setup** (Assumed separate repo):
   - Run backend server on `http://localhost:5000`.
   - Ensure API endpoints available (auth/leads/etc.).
5. **Environment**:
   - No `.env` visible; add if needed (e.g., `REACT_APP_API_URL`).
6. **Run Development**:
   ```
   npm start
   ```
   - Opens `http://localhost:3000`.
7. **Build**:
   ```
   npm run build
   ```
   - Outputs to `/build` folder.

### Environment Configuration
- **API Base**: `src/config/api.js` → `http://localhost:5000/api` (dev/prod same; update for prod).
- **Secrets**: Admin auth uses JWT token (localStorage). Use HTTPS in prod.
- **Custom**: Tailwind config extends colors/fonts/shadows.

**Recommended .env** (create `Savorka_frontend/.env`):
```
REACT_APP_API_URL=http://your-api-domain.com/api
```

### Code Structure and Folder Organization
```
Savorka_frontend/
├── public/              # Static assets (index.html, favicon)
├── src/
│   ├── admin/           # Admin dashboard (Dashboard.jsx, ViewLeads.jsx, etc.)
│   ├── assets/          # Images (projects, services, logos)
│   ├── auth/            # AuthContext.jsx, ProtectedRoute.jsx
│   ├── components/      # Reusable UI (Navbar, Footer, HeroSection, SavorkaBot)
│   ├── config/          # api.js (base URL)
│   ├── data/            # Static data (projectsData.jsx, servicesData.jsx)
│   ├── pages/           # Route components (HomePage, AboutPage, GoSolar)
│   ├── styles/          # index.css (Tailwind + custom)
│   ├── App.js           # Root + Routes
│   └── index.js         # Entry + Providers
├── package.json         # Deps + scripts
├── tailwind.config.js   # Custom theme
└── README.md            # Basic setup
```
- **~50 components**: Modular (e.g., SolarCarousel, TestimonialsSection).
- **Pages**: Assemble components (e.g., HomePage imports HeroSection, ServicesSection).
- **Admin**: Separate layout with sidebar, real-time polling.

### API Documentation
**Base URL**: `http://localhost:5000/api`

#### Authentication
- **POST /auth/login**
  - Body: `{email, password}`
  - Response: `{token, admin: {name}}` (200) | `{message}` (401)
  - Auth: JWT in localStorage.

#### Leads Management
- **GET /leads?category=Residential** → `[{_id, name, whatsapp, pincode, bill, status, ...}]`
- **PATCH /leads/:id/status** Body: `{status: "Pending|Connected|Rejected"}`
- **DELETE /leads/:id**

#### Other
- **GET /contact-leads** → Contact form submissions.
- **GET /newsletters** → Subscriber list.
- **GET /comments/count** → `{total}`.

**Examples** (using fetch):
```js
// Login
fetch(`${API_BASE_URL}/auth/login`, {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  body: JSON.stringify({email, password})
});

// Get Leads
fetch(`${API_BASE_URL}/leads?category=Residential`).then(r => r.json());
```

*Assumption: Backend schemas include leads (status enum), contacts, etc. No full OpenAPI spec available.*

### Database Schema and Models (Assumed from API/Frontend)
No direct DB access (frontend-only). Inferred schemas:
- **Leads**: `{ _id, name, whatsapp, pincode, bill/monthlyBill/commercialBill, status: enum['Pending','Connected','Rejected'], category: 'Residential|Housing Society|Commercial', societyName?, agmStatus?, designation?, city?, companyName? }`
- **ContactLeads**: `{ name?, email?, phone?, message? }` (from ContactPage form).
- **Newsletters**: `{ email? }`
- **Comments**: `{ total count }` (blog?).

*Label: Assumptions based on admin fetches/forms. Backend defines full schemas.*

### Key Workflows and Logic
1. **Lead Capture**: Forms → POST /leads → Admin dashboard polls → Toast notifications.
2. **Auth Flow**: `/admin` → Login → localStorage token → ProtectedRoute → Dashboard.
3. **Public Navigation**: Navbar + React Router. SavorkaBot (floating chat?).
4. **Preloader**: Custom SavorkaPreloader on first load.
5. **Responsive**: Tailwind breakpoints (mobile <640px, etc.).

### Deployment Process
1. **Build**: `npm run build`.
2. **Hosting** (Static SPA):
   - **Vercel**: `vercel --prod` (auto deploys from Git).
   - **Netlify**: Drag `/build` folder.
   - **GitHub Pages**: Use `gh-pages` package.
3. **CI/CD** (Recommended): GitHub Actions workflow for auto-build/deploy.
4. **Backend**: Deploy separately (ensure CORS allows frontend domain).
5. **Scaling**: CDN for assets (Cloudflare). No server-side needed.

**Sample GitHub Actions (.github/workflows/deploy.yml)**:
```yaml
name: Deploy
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm ci
      - run: npm run build
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build
```

### Testing Strategy and How to Run Tests
*Current: No tests implemented.*
- **Recommended**: Jest + React Testing Library for components.
- **E2E**: Cypress/Playwright for flows (form submit → API mock).
- **Add**: `npm test` script.

To implement:
```
npm install --save-dev @testing-library/react jest
```

### Contribution Guidelines
- **Branching**: `feature/feat-name`, `bugfix/issue-123`. PR to `main`.
- **Standards**: ESLint/Prettier (add if missing). 2-space indent, functional components/hooks.
- **Commit**: Conventional (feat:, fix:, docs:). Semantic versioning.
- **PR**: Describe changes, link issues. CI checks + review.
- **Pull Requests**: Use GitHub PR template.

### Troubleshooting for Developers
| Issue | Solution |
|-------|----------|
| Tailwind styles missing | Restart dev server; check `content` in tailwind.config.js. |
| API 404/CORS | Ensure backend running on :5000; update api.js. |
| Admin auth fails | Check localStorage 'token'/'admin'; backend login endpoint. |
| Build fails | `rm -rf node_modules package-lock.json && npm install`. |
| Animations lag | Reduce Framer Motion complexity on mobile. |

## 2. Support Documentation

### Product Overview (Non-Technical)
**Savorka Solar** is your go-to website for switching to clean, affordable solar energy. Whether you're a homeowner, housing society, or business owner, Savorka helps you save on electricity bills with custom solar installations. Explore services, view real projects, get free consultations, and let admins handle your inquiries efficiently.

### Key Features and How to Use Them
- **Home**: See hero banners, why solar, services, testimonials.
- **Services**: Learn about rooftop solar, ground-mounted systems.
- **Projects**: Browse 10+ completed solar projects (e.g., 5.2MW Jasingpura).
- **GoSolar/Contact**: Fill forms for quotes → Get callback.
- **Blog**: Read solar tips (if implemented).
- **Admin** (Staff): Login at `/admin` to view/manage leads.

### User Guides (Step-by-Step)
**Get a Solar Quote**:
1. Visit savorka.com/contact or /gosolar.
2. Select category (Residential/Commercial).
3. Enter name, WhatsApp, pincode, bill amount.
4. Submit → Expect call within 24 hours.

**Subscribe to Newsletter**:
1. Scroll to footer.
2. Enter email → Confirm.

**For Housing Societies**:
- Provide society name, AGM status, your designation.

### FAQs
**Q: How long until installation?**  
A: 4-8 weeks post-approval (depends on size).

**Q: What's the warranty?**  
A: 25 years on panels, 5 years inverter (assumed).

**Q: Do I need approvals?**  
A: We handle discom approvals/net metering.

**Q: Cost savings?**  
A: 20-40% bill reduction.

### Common Issues and Resolutions
| Issue | Resolution |
|-------|------------|
| Form not submitting | Check internet; WhatsApp field required. |
| No callback | Email support@savorka.com. |
| Site slow | Clear cache; use Chrome. |

### Error Message Explanations
- **"Email not found"**: Use registered admin email.
- **"Password incorrect"**: Reset via backend.
- **"No leads found"**: Filter by category; wait for submissions.
- **Network error**: Backend down; contact IT.

### Account and Access Management
- **Admin**: Email/password at `/admin`. Contact support for access.
- **Users**: No accounts; form-based leads.

### Contact and Escalation Process
- **Primary**: Form on site → Admin review.
- **Email**: support@savorka.com *(assumed)*.
- **Escalation**: Director → priya@savorka.com *(from assets)*.
- **Response**: <24h for leads, 48h for queries.

### SLA or Response Expectations
- New leads: Admin notified instantly, response <24h.
- Site uptime: 99.5% *(hosting-dependent)*.
- Lead management: Daily dashboard checks.

---


