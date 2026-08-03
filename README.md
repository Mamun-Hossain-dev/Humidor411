# Humidor411

Humidor411 is a full-stack digital operating platform for premium cigar
retailers. It connects day-to-day humidor and inventory management with a
customer-facing store experience, helping retailers organize products,
identify sales opportunities, and guide customers to the right cigar.

The repository contains a Next.js web application and a NestJS REST API. The
platform supports three connected experiences:

- **Retailers** manage their store, humidors, shelves, inventory, featured
  products, sales, and business insights.
- **Customers** browse a retailer's live inventory, search by preference,
  discover recommendations, save favorites, and locate cigars inside the
  store.
- **Admins** manage the shared cigar catalog and use backend tools for retailer,
  user, subscription, payment, content, and platform oversight.

## Why the Platform Stands Out

- **Built specifically for cigar retail:** Inventory records include cigar
  strength, wrapper, size, smoking time, pairing suggestions, and exact humidor
  placement.
- **Master Database workflow:** A shared cigar catalog keeps product data
  consistent across retailers. Retailers can select an existing master cigar
  when adding inventory, while admins can create, review, update, search, or
  bulk-import catalog records.
- **Exact shelf mapping:** Every inventory item can be assigned to a humidor,
  shelf, row, and column, making in-store product location part of the customer
  experience.
- **Actionable inventory management:** Low-stock thresholds, out-of-stock
  states, slow-stock opportunities, discounts, recorded sales, and notification
  support help retailers act on operational data.
- **Discovery-driven customer experience:** Public store pages include guided
  cigar discovery, customer-style search, surprise recommendations, staff
  picks, new arrivals, daily featured products, related cigars, and exclusive
  picks.
- **Retail intelligence:** Retailer dashboards expose revenue, units sold,
  average order value, sales trends, top products, strength distribution, and
  a focused “What should I do today?” action center.

## Core Backend Features

### Master Cigar Database

The Master Database acts as the platform-wide source of cigar information. It
supports:

- Search, filtering, sorting, and pagination
- Cigar name, brand, manufacturer, country, description, price, and status
- Admin-controlled create, update, and delete operations
- Spreadsheet-based bulk upload
- Retailer-submitted or under-review cigar records
- Linking retailer inventory to a shared master cigar through `masterCigarId`

This model reduces repeated data entry while still allowing a retailer to add
store-specific pricing, quantity, placement, and merchandising information.

### Inventory and Humidor Operations

Retailers can:

- Create multiple humidors and configure shelves with row/column grids
- Add inventory from the Master Database or enter a custom cigar
- Track quantity, retail price, status, and low-stock thresholds
- Record sales and maintain sales history
- Assign exact humidor, shelf, row, and column locations
- Add cigar metadata such as strength, wrapper, size, smoking time, and
  pairings
- Manage discounts and slow-moving inventory opportunities
- Promote inventory as Staff Picks, New Arrivals, or Daily Featured products
- Schedule expiry and automatic cleanup for time-sensitive promotions

### Authentication and Account Lifecycle

The API includes JWT-based authentication, password hashing, registration,
login, OTP verification, forgot/reset password, change password, profile
management, and role-based authorization. Supported roles include `retailer`,
`admin`, and `customer`.

### Retailer, Subscription, and Payment Support

Retailer profiles contain store identity, address, public slug, logo, banner,
QR code, approval status, and subscription state. Stripe-backed subscription
and payment modules, webhook handling, Cloudinary uploads, email helpers, and
notification APIs support the wider account lifecycle.

### Admin Capabilities

The backend exposes protected admin APIs for:

- User and retailer listing, filtering, review, and status management
- Master Database maintenance and bulk imports
- Subscription and payment visibility
- Platform overview, earnings, retailer counts, pending products, and revenue
  reporting
- Landing-page content such as banners, retailer information, platform
  features, workflow steps, and business benefits

The current repository primarily delivers the retailer dashboard and public
store UI; the backend admin APIs provide the foundation for a dedicated admin
interface.

## Main User Flows

### Retailer Flow

1. Register and verify an account.
2. Complete retailer onboarding and store information.
3. Select a subscription and complete payment.
4. Create a humidor, shelves, and shelf grids.
5. Add inventory using a Master Database cigar or a custom record.
6. Track stock, record sales, manage promotions, and review opportunities.
7. Share the store QR code and public store link with customers.
8. Use dashboard insights to make merchandising and purchasing decisions.

### Customer Flow

1. Open a retailer's public store through its slug or QR code.
2. Browse all products or explore Staff Picks, New Arrivals, and Daily
   Featured cigars.
3. Search directly, complete the guided discovery quiz, or request a surprise
   recommendation.
4. View cigar details, availability, price, pairings, and exact shelf location.
5. Save favorites or share a cigar link.

### Admin Flow

1. Review users, retailers, subscriptions, and platform activity.
2. Maintain and bulk-import the Master Cigar Database.
3. Monitor pending products, retailer approvals, revenue, and payments.
4. Manage public landing-page content through the related content APIs.

## Technology Stack

| Layer | Technologies |
| --- | --- |
| Frontend | Next.js 14, React 18, TypeScript, Tailwind CSS |
| UI and data | Radix UI, TanStack Query, React Hook Form, Zod |
| Authentication | NextAuth on the web, JWT and bcrypt in the API |
| Backend | NestJS 11, TypeScript, REST, Swagger |
| Database | MongoDB, Mongoose |
| Media | Multer, Cloudinary |
| Payments | Stripe and Stripe webhooks |
| Supporting services | Nodemailer, QR Code, PDFKit, XLSX, cron jobs |

## Repository Structure

```text
baloose/
├── beloose-website/             # Next.js frontend
│   ├── public/                  # Static images and assets
│   └── src/
│       ├── app/                 # Routes and layouts
│       ├── components/          # Shared, website, and dashboard UI
│       ├── hooks/               # Client hooks
│       └── lib/                 # API clients and domain helpers
└── beloose561_backend_nestjs/   # NestJS API
    └── src/
        └── app/
            ├── helpers/         # Upload, email, pagination, QR, and cron logic
            ├── middlewares/     # Authentication and global errors
            └── module/          # Domain modules
```

## Production Deployment

Production deployment is streamlined with `docker-compose.prod.yml`, which
starts the backend API, customer website, and admin dashboard together using
prebuilt Docker images:

```bash
docker compose -f docker-compose.prod.yml up -d
```

Each application Dockerfile uses a multi-stage build. Dependencies, application
compilation, and the production runtime are kept in separate stages so the
final images contain only the files and packages required to run in production,
resulting in smaller and more secure containers.

## Local Development

### Requirements

- Node.js 20 or newer
- npm
- MongoDB locally or through MongoDB Atlas

### 1. Start the Backend

```bash
cd beloose561_backend_nestjs
npm install
```

Create `beloose561_backend_nestjs/.env` with the required values:

```env
NODE_ENV=development
PORT=8080
APP_NAME=Humidor411
MONGO_URI=mongodb://127.0.0.1:27017/humidor411
FRONTEND_URL=http://localhost:3000

BCRYPT_SALT_ROUNDS=10
ACCESS_TOKEN_SECRET=replace-with-a-secure-secret
ACCESS_TOKEN_EXPIRES=7d
REFRESH_TOKEN_SECRET=replace-with-another-secure-secret
REFRESH_TOKEN_EXPIRES=90d
```

Cloudinary, email, and Stripe environment variables are required when using
uploads, transactional email, subscriptions, or payments.

```bash
npm run start:dev
```

The API will be available at `http://localhost:8080/api/v1`, with Swagger
documentation at `http://localhost:8080/api/docs`.

### 2. Start the Frontend

```bash
cd beloose-website
npm install
```

Create `beloose-website/.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:8080/api/v1
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=replace-with-a-secure-secret
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=
```

Then start the development server:

```bash
npm run dev
```

Open `http://localhost:3000`.

## Useful Commands

| Project | Command | Purpose |
| --- | --- | --- |
| Frontend | `npm run dev` | Start the Next.js development server |
| Frontend | `npm run build` | Create a production build |
| Frontend | `npm run start` | Run the production build |
| Backend | `npm run start:dev` | Start NestJS in watch mode |
| Backend | `npm run build` | Compile the API |
| Backend | `npm run start:prod` | Run the compiled API |
| Backend | `npm test` | Run unit tests |
| Backend | `npm run test:e2e` | Run end-to-end tests |

## API Conventions

- REST endpoints use the `/api/v1` prefix.
- Protected endpoints accept a Bearer access token.
- Request DTOs are validated globally.
- API responses use a consistent status, message, metadata, and data shape.
- Swagger documents available endpoints and authorization requirements.
- Collection endpoints commonly support pagination, search, filtering, and
  sorting.
