## AI Finance Platform (Next.js, Prisma, Clerk, Inngest, Arcjet)

An AI-augmented personal finance platform built with Next.js App Router. It features account and transaction management, budgeting, analytics dashboards, receipt scanning (Gemini), background jobs (Inngest), security/rate-limiting (Arcjet), and transactional emails (Resend). Authentication is powered by Clerk.

### Features
- **Authentication (Clerk)**: Sign in/up flows at `/sign-in` and `/sign-up` with protected routes.
- **Accounts**: Create and manage bank accounts with types and balances.
- **Transactions**: Create, list, and manage income/expense transactions, including categories and statuses.
- **Budgets**: Set a monthly budget; get progress and alerts when overspending.
- **Dashboard**: Charts and summaries of recent activity, spend vs income, and account cards.
- **Receipt Scanner (Gemini)**: Upload receipts and parse amounts/categories using Google Gemini.
- **Emails (Resend)**: Email templates for notifications.
- **Background Jobs (Inngest)**: Scheduled or triggered workflows.
- **Security (Arcjet)**: Basic bot/abuse protection for routes.
- **UI/UX**: Tailwind CSS + shadcn/ui components.

### Tech Stack
- **Framework**: Next.js 15 (App Router), React 19 RC
- **Database/ORM**: PostgreSQL + Prisma 6
- **Auth**: Clerk
- **AI**: `@google/generative-ai` (Gemini)
- **Emails**: Resend + `@react-email/components`
- **Jobs**: Inngest
- **Security**: Arcjet
- **UI**: Tailwind CSS, shadcn/ui, Radix UI, Recharts

### Environment Variables
Create a `.env` at the project root with:

```bash
# Database (Postgres)
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DBNAME"
DIRECT_URL="postgresql://USER:PASSWORD@HOST:PORT/DBNAME"

# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/dashboard
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/dashboard

# Gemini
GEMINI_API_KEY=

# Resend
RESEND_API_KEY=

# Arcjet
ARCJET_KEY=
```

### Getting Started (Local Development)
1) **Install dependencies**

```bash
npm install
```

2) **Generate Prisma client** (runs on postinstall, but safe to run)

```bash
npx prisma generate
```

3) **Run database migrations**

```bash
npx prisma migrate dev
```

4) **Start the dev server**

```bash
npm run dev
```

App runs at `http://localhost:3000`.

### Database & Seeding
- Prisma schema is in `prisma/schema.prisma` with models: `User`, `Account`, `Transaction`, `Budget`.
- Optional seed endpoint: `GET /api/seed` calls server action `seedTransactions()` to generate ~90 days of sample transactions.
  - Note: `actions/seed.js` uses hardcoded `ACCOUNT_ID` and `USER_ID`. Update these constants to valid IDs in your DB before seeding, or adapt the script to look up the default account for the current user.

### Notable Directories
- `app/(auth)`: Auth routes (`/sign-in`, `/sign-up`) and layout
- `app/(main)/dashboard`: Dashboard pages and components
- `app/(main)/account` and `app/(main)/transaction`: Account views and transaction CRUD
- `actions/`: Server Actions for accounts, transactions, budget, dashboard, email
- `lib/`: Prisma client, utils, Inngest, Arcjet
- `components/`: Shared UI (including shadcn/ui wrappers)

### Email & Background Jobs
- **Emails**: Templates in `emails/template.jsx`. Configure `RESEND_API_KEY` and use the `send-email` server action.
- **Inngest**: Client and functions under `lib/inngest`. API route mounted at `/api/inngest`.

### Security
- **Arcjet**: Config in `lib/arcjet.js`; add middleware/usage where appropriate to protect routes.
- **Clerk Middleware**: Configured in `middleware.js` for auth-protected routes.

### Scripts
```json
{
  "dev": "next dev --turbopack",
  "build": "next build",
  "start": "next start",
  "lint": "next lint",
  "postinstall": "prisma generate"
}
```

### Deployment
- **Vercel (recommended)**: Set all environment variables. Attach a managed Postgres (e.g., Neon, Supabase) and update `DATABASE_URL`/`DIRECT_URL`.
- Run `npx prisma migrate deploy` on deploy.
- Ensure Clerk, Resend, Arcjet, and Gemini keys are set in the hosting environment.

### Troubleshooting
- Prisma connection issues: verify `DATABASE_URL`/`DIRECT_URL` and that the DB is reachable.
- Clerk redirects: make sure `NEXT_PUBLIC_CLERK_*` URLs match routes in `app/(auth)` and after-sign-in routes exist (`/dashboard`).
- Seed script: update `ACCOUNT_ID`/`USER_ID` in `actions/seed.js` to real IDs before calling `/api/seed`.

### Highlights
- Built full‑stack AI finance platform to track income, expenses, budgets, and multi‑account balances.
- Integrated AI receipt scanning and auto‑categorization via Gemini to streamline transaction entry.
- Added budget alerts, recurring transactions (cron/background jobs), and monthly insights.
- Robust transaction CRUD with validation (React Hook Form + Zod), search/filter, and bulk actions.
- Secure auth with Clerk (Google login, protected routes) and Arcjet hardening; deployed on Vercel.
- Interactive analytics with charts and AI-driven spending insights.
