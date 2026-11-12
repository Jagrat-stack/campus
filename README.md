# CampusMart 🎓🛒

**CampusMart** is a reliable, production-ready, second-hand marketplace exclusively for verified students of one college. Built with **Next.js 15 (App Router)**, **TypeScript (Strict Mode)**, **Tailwind CSS**, and **Supabase (PostgreSQL, Auth, Storage, Realtime)**.

---

## 🚀 Key Features

1. **Strict College Email Authentication**:
   - Only emails ending in `@<COLLEGE_DOMAIN>` (e.g. `@campus.edu`) can sign up and participate.
   - Enforced on the database level via a PostgreSQL trigger (`BEFORE INSERT ON auth.users`) and verified across all server actions.
   - Requires email confirmation before access.
2. **Student Profiles**:
   - Name, department, year of study (Year 1 to 6), campus residency status (Hostel / Day Scholar), hostel name, and avatar.
3. **Sell Flow**:
   - Title, price (INR), category, condition (*Brand New*, *Like New*, *Good*, *Fair*), description, campus pickup location.
   - Multi-image uploader (up to 5 photos) with **client-side HTML5 Canvas compression** to keep uploads under 800KB before storing in Supabase Storage.
4. **Browse & Search**:
   - Responsive grid, horizontal category chips, condition filters, price range filters, full-text search, and pagination.
5. **Item Detail Page**:
   - Image gallery with thumbnails, student seller card, save to wishlist, "Chat with Seller" action, and "Report Listing" modal.
6. **Realtime 1-on-1 Chat**:
   - Powered by Supabase Realtime Postgres CDC channels.
   - Conversation sidebar with unread badge count and last message snippet.
   - Message bubble view with timestamps and delivery/read receipts.
7. **My Listings Management**:
   - View active and sold listings.
   - Inline edit listing modal.
   - Mark as sold / Re-list toggle.
   - Permanent delete listing with cascade cleanup.
8. **Wishlist**:
   - Dedicated saved items page with instant optimistic toggling.
9. **Reports & Admin Moderation Panel**:
   - Protected route (`/admin`) restricted to `role = 'admin'` profiles.
   - Review user reports and complaints.
   - One-click "Remove Listing" (soft hides item) and "Ban Seller" (suspends student account).
   - Dedicated User Moderation table to ban/unban students.

---

## 🛠️ Tech Stack

- **Framework**: Next.js 15 (App Router, Server Actions, Route Handlers)
- **Language**: TypeScript (`strict: true`, no `any`)
- **Styling**: Tailwind CSS + Radix UI / custom accessible components
- **Database & Auth**: Supabase (PostgreSQL, Storage, Realtime, RLS)
- **Validation**: Zod + React Hook Form
- **Toasts**: Sonner
- **Testing**: Vitest + React Testing Library + Playwright E2E
- **Tooling**: ESLint + Prettier + Husky + lint-staged

---

## 📂 Project Structure

```
campusmart/
├── .env.example                               # Environment variable documentation
├── .eslintrc.json                             # Next.js ESLint configuration
├── .husky/pre-commit                          # Git pre-commit hook (lint-staged)
├── .lintstagedrc.json                         # Automated lint & format on commit
├── .prettierrc                                # Code formatting rules
├── playwright.config.ts                       # E2E test suite configuration
├── vitest.config.ts                           # Unit test configuration
├── supabase/
│   ├── seed.sql                               # Default categories & domain seed
│   └── migrations/
│       ├── 20261103000001_create_profiles_and_auth_trigger.sql
│       ├── 20261103000002_create_categories_and_items.sql
│       ├── 20261103000003_create_conversations_and_messages.sql
│       ├── 20261103000004_create_wishlist_and_reports.sql
│       ├── 20261103000005_storage_buckets_and_policies.sql
│       └── 20261103000006_full_text_search_and_indexes.sql
├── tests/
│   ├── unit/
│   │   ├── auth-domain.test.ts                # College email domain validation tests
│   │   ├── image-compression.test.ts          # File size & MIME validation tests
│   │   └── zod-schemas.test.ts                # Zod boundary and schema tests
│   └── e2e/
│       ├── auth.spec.ts                       # E2E authentication & gate tests
│       └── marketplace.spec.ts                # E2E browse, filter, & navigation tests
└── src/
    ├── actions/                               # Next.js Server Actions
    │   ├── auth.ts                            # Signup, login, logout, profile update
    │   ├── chat.ts                            # Send message, create chat, mark read
    │   ├── items.ts                           # Create, update, delete, toggle sold
    │   ├── reports.ts                         # File report, admin resolve, ban user
    │   └── wishlist.ts                        # Toggle wishlist item
    ├── app/                                   # Next.js App Router pages
    │   ├── layout.tsx                         # Root layout with Navbar, Footer & Toaster
    │   ├── page.tsx                           # Marketplace homepage with filters & grid
    │   ├── (auth)/login/page.tsx              # Student login
    │   ├── (auth)/signup/page.tsx             # Student signup with domain validation
    │   ├── (auth)/verify-email/page.tsx       # Email verification prompt
    │   ├── (auth)/auth/callback/route.ts      # Supabase OAuth/email callback handler
    │   ├── items/[id]/page.tsx                # Item detail view
    │   ├── items/new/page.tsx                 # Sell item form
    │   ├── my-listings/page.tsx               # Manage user's listings
    │   ├── wishlist/page.tsx                  # User's saved items
    │   ├── messages/                          # Realtime 1-on-1 chat layout & room
    │   ├── profile/page.tsx                   # Student profile settings
    │   └── admin/                             # Admin reports dashboard & user moderation
    ├── components/                            # Reusable React components
    │   ├── ui/                                # Button, Input, Textarea, Card, Badge, Dialog
    │   ├── layout/                            # Navbar, Footer
    │   ├── items/                             # ItemCard, ItemGrid, ItemFilters, SellForm, Gallery
    │   ├── chat/                              # ConversationList, ChatWindow
    │   ├── admin/                             # ReportReviewCard, UserModerationTable
    │   └── shared/                            # EmptyState, Skeleton, ErrorBoundary
    ├── lib/
    │   ├── supabase/                          # Browser, Server, Admin & Middleware clients
    │   ├── types/                             # Database & Marketplace TypeScript definitions
    │   ├── utils/                             # Canvas image compressor, formatters, cn
    │   └── validations/                       # Zod schemas (auth, items, chat, reports)
    └── middleware.ts                          # Session refresh & protected route guard
```

---

## ⚡ Getting Started

### 1. Prerequisites
- **Node.js**: v18.18 or higher (tested on v22.14)
- **A Supabase Project**: Free tier at [supabase.com](https://supabase.com)

### 2. Clone and Install Dependencies
```bash
git clone <your-repo-url>
cd campusmart
npm install
```

### 3. Supabase Database & Storage Setup
1. Open your Supabase project dashboard -> **SQL Editor**.
2. Run each migration file in `supabase/migrations/` in sequential order:
   - `20261103000001_create_profiles_and_auth_trigger.sql`
   - `20261103000002_create_categories_and_items.sql`
   - `20261103000003_create_conversations_and_messages.sql`
   - `20261103000004_create_wishlist_and_reports.sql`
   - `20261103000005_storage_buckets_and_policies.sql`
   - `20261103000006_full_text_search_and_indexes.sql`
3. Run the seed script:
   - `supabase/seed.sql`
4. Under **Authentication -> URL Configuration**:
   - Set **Site URL** to `http://localhost:3000` (or your production Vercel URL).
   - Add Redirect URL: `http://localhost:3000/auth/callback`.

### 4. Configure Environment Variables
Copy `.env.example` to `.env.local`:
```bash
cp .env.example .env.local
```
Fill in your credentials:
```env
NEXT_PUBLIC_COLLEGE_DOMAIN="campus.edu"
NEXT_PUBLIC_COLLEGE_NAME="City Tech Campus"
NEXT_PUBLIC_SUPABASE_URL="https://your-project-id.supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="your-anon-key"
SUPABASE_SERVICE_ROLE_KEY="your-service-role-key"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

### 5. Run the Local Development Server
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## 🧪 Testing & Quality Assurance

### Run Unit Tests (Vitest)
Executes unit tests for college domain verification, file size/MIME compression rules, and Zod schemas:
```bash
npm test
```

### Run Linter & Formatter
```bash
npm run lint
npm run format
```

### Run End-to-End Tests (Playwright)
```bash
npm run test:e2e
```

---

## 👑 Assigning an Admin
To grant moderator access to a verified student account:
```sql
update public.profiles
set role = 'admin'
where email = 'your-email@campus.edu';
```
The user will now have access to `/admin` to review reports and ban offending accounts.

---

## 🚢 Deploying to Vercel

1. Push your code to a GitHub repository:
   ```bash
   git add .
   git commit -m "Initial commit of CampusMart marketplace"
   git remote add origin <your-github-repo-url>
   git push -u origin main
   ```
2. Import the repository in [Vercel](https://vercel.com).
3. Under **Environment Variables**, add:
   - `NEXT_PUBLIC_COLLEGE_DOMAIN`
   - `NEXT_PUBLIC_COLLEGE_NAME`
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `SUPABASE_SERVICE_ROLE_KEY`
   - `NEXT_PUBLIC_APP_URL` (set to your Vercel deployment URL)
4. Click **Deploy**.
