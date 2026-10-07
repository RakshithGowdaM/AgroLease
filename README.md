# AgroLease

AgroLease is a full-stack agricultural equipment rental marketplace that connects farmers who need machinery with equipment owners who want to rent it out. Users can discover nearby equipment, compare rental options, check availability, place bookings, and manage the rental lifecycle from responsive dashboards.

The project is designed for local agricultural communities where tractors, harvesters, irrigation systems, tillers, and other specialized equipment may be expensive to purchase or difficult to access. It provides a shared digital marketplace for short-term access, owner-managed listings, booking coordination, delivery planning, and operational support.

## What the Platform Provides

### For farmers

- Browse equipment by category, location, price, and availability.
- Search and sort listings from the public marketplace.
- View equipment details, images, pricing, reviews, and owner information.
- Book equipment by day or hour for a selected date and time range.
- Choose delivery or self-pickup where supported.
- Add optional operator or driver charges.
- View booking history, statuses, spending statistics, and cancellation options.
- Submit support tickets and manage profile information.

### For equipment owners

- Create, edit, and remove equipment listings.
- Upload listing images and maintain equipment information.
- Set rental prices, availability, location, and delivery options.
- Review incoming bookings and update their status.
- Monitor listing performance and booking activity.
- View delivery and live-location information where enabled.
- Manage support requests from the owner dashboard.

### For administrators

- Review platform activity and revenue statistics.
- Manage users, verification, suspension, and account removal.
- Approve and manage equipment listings.
- Monitor and manage bookings across the platform.
- Manage support tickets.
- Maintain chatbot frequently asked questions.

## Main Features

- **Equipment marketplace:** Searchable listings with category filters, availability checks, pricing, images, and reviews.
- **Booking management:** Booking creation and lifecycle statuses including pending, confirmed, active, completed, and cancelled.
- **Flexible rental pricing:** Supports hourly and daily rental periods, delivery charges, and optional operator costs.
- **Location-aware discovery:** Leaflet-based maps for equipment discovery, farmer bookings, owner delivery locations, and distance-based delivery planning.
- **Role-based dashboards:** Separate experiences for farmers, equipment owners, and administrators.
- **Authentication:** Registration, login, logout, JWT authentication, role authorization, profile updates, password changes, and password reset flows.
- **Multiple browser sessions:** The frontend can retain recent account sessions and allow quick account switching on the same device.
- **Support center:** Users can create and track support tickets while administrators manage them centrally.
- **Reviews and translations:** Equipment content can be reviewed and translated through the available services.
- **Chatbot assistant:** An embedded assistant can use configured AI credentials or fall back to managed FAQ responses.
- **API documentation:** OpenAPI JSON and Swagger UI are available from the backend.
- **Deployment readiness:** Health and readiness endpoints, security middleware, request logging, rate limiting, and a Render Blueprint are included.

## Technology Stack

### Frontend

- React 18
- Vite
- React Router
- Axios
- Leaflet and React Leaflet
- Socket.IO client
- Lucide React icons
- `react-hot-toast` for notifications

### Backend

- Node.js 18 or newer
- Express
- MongoDB with Mongoose
- JWT and bcrypt-based authentication
- Socket.IO for real-time communication
- Cloudinary integration for image uploads
- Express Validator, Helmet, CORS, rate limiting, and request sanitization
- Swagger UI and OpenAPI documentation

## Repository Structure

```text
.
├── frontend/                 # React + Vite web application
│   ├── public/
│   └── src/
│       ├── components/       # Shared UI and marketplace components
│       ├── context/          # Authentication and language state
│       ├── pages/            # Marketplace, dashboards, auth, and profile pages
│       ├── services/         # API and domain service clients
│       └── utils/            # Browser storage and shared utilities
├── backend/                  # Node.js + Express API
│   ├── src/
│   │   ├── config/           # Database, environment, and Cloudinary setup
│   │   ├── controllers/      # Request handlers
│   │   ├── middleware/       # Auth, validation, uploads, and errors
│   │   ├── models/           # MongoDB models
│   │   ├── routes/            # API route definitions
│   │   └── services/         # Backend services
│   └── tests/
├── render.yaml               # Render deployment blueprint
└── package.json              # Workspace-level scripts
```

## Getting Started

### Prerequisites

- Node.js 18 or newer
- npm
- A MongoDB database for backend persistence

### Install dependencies

From the repository root:

```bash
npm install
npm install --prefix frontend
npm install --prefix backend
```

Create the backend environment file from the example provided in the backend directory, then configure the required values before starting the server.

### Run in development

Start both applications together:

```bash
npm run dev
```

Or start them independently:

```bash
npm run dev:backend
npm run dev:frontend
```

The frontend is served by Vite and the backend listens on port `5000` by default. Set `VITE_API_URL` to the backend API base URL when the frontend is not using its local default.

### Default demo accounts

After running `npm run seed:backend`, use these accounts to explore each role:

| Role | Email | Password |
| --- | --- | --- |
| Farmer | `farmer@demo.com` | `demo123` |
| Equipment owner | `owner@demo.com` | `demo123` |
| Administrator | `admin@demo.com` | `demo123` |

These credentials are for local/demo use only. Change or remove seeded accounts before deploying a shared or production environment.

### Available workspace scripts

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the frontend and backend together |
| `npm run dev:frontend` | Start the Vite development server |
| `npm run dev:backend` | Start the Express server with nodemon |
| `npm run build:frontend` | Create a production frontend build |
| `npm run preview:frontend` | Preview the production frontend build |
| `npm run start:backend` | Start the backend in production mode |
| `npm run seed:backend` | Seed the backend database |

## Environment Variables

### Backend required variables

```env
NODE_ENV=development
PORT=5000
MONGO_URI=your-mongodb-connection-string
JWT_SECRET=replace-with-a-long-random-secret
JWT_EXPIRE=30d
JWT_COOKIE_EXPIRE=30
CLIENT_URL=http://localhost:5173
```

### Backend optional integrations

```env
CLOUDINARY_CLOUD_NAME=...
CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...
GEMINI_API_KEY=...
OPENAI_API_KEY=...
```

Cloudinary variables enable hosted image uploads. AI variables enable AI-assisted chatbot responses; without them, the chatbot can use configured FAQ or fallback responses.

### Frontend

```env
VITE_API_URL=http://localhost:5000/api
```

Do not commit real credentials or production secrets. Use the deployment provider's secret manager for hosted environments.

## API Surface

The backend groups functionality under these route prefixes:

| Prefix | Responsibility |
| --- | --- |
| `/api/auth` | Registration, login, profiles, password operations |
| `/api/equipment` | Listings, availability, images, reviews, and locations |
| `/api/bookings` | Booking creation, status changes, cancellation, and statistics |
| `/api/admin` | Administration, analytics, approvals, and chatbot FAQs |
| `/api/support` | Support ticket creation and management |
| `/api/chatbot` | Chatbot questions and responses |
| `/api/translate` | Translation requests |

Operational and documentation endpoints:

- `GET /api/health` - liveness check
- `GET /api/ready` - readiness check
- `GET /api/openapi.json` - OpenAPI specification
- `GET /api/docs` - Swagger UI

## Deployment with Render

The repository includes `render.yaml` for deploying two services:

1. A Node.js backend using `backend/` as its root directory.
2. A static frontend using `frontend/` as its root directory.

Configure the backend's `MONGO_URI`, `JWT_SECRET`, and `CLIENT_URL`, then set the frontend's `VITE_API_URL` to the deployed backend URL, for example:

```text
https://your-backend.onrender.com/api
```

The backend readiness check is `/api/ready`, and the frontend uses an SPA rewrite so client-side routes resolve correctly. Render's free tier may suspend inactive services, which can cause a delay on the first request after inactivity.

## Data and Security Notes

- MongoDB is the authoritative store for users, equipment, bookings, and support data.
- Browser storage is used for frontend session compatibility and local client-side state; it is device-specific and should not be treated as shared persistence.
- Payment fields and booking payment statuses are supported, but a payment gateway is not included in this repository.
- Password reset currently exposes a reset URL through the API flow; production deployments should connect this to a secure email delivery service.
- Use HTTPS, strong secrets, restricted CORS origins, and managed environment variables in production.

## Testing

Run backend tests with:

```bash
npm test --prefix backend
```

Run a production frontend build with:

```bash
npm run build:frontend
```

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
