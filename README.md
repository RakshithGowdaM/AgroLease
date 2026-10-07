# Agro Rent Workspace

Professional split structure:

- `frontend/` - React + Vite client app
- `backend/` - Node.js + Express API server

## Run Commands (from workspace root)

- Start frontend: `npm run dev:frontend`
- Start backend: `npm run dev:backend`
- Build frontend: `npm run build:frontend`
- Preview frontend build: `npm run preview:frontend`
- Start backend (production mode): `npm run start:backend`
- Seed backend data: `npm run seed:backend`

## Render Deployment

This repository includes a Render Blueprint at `render.yaml` for one backend web service and one frontend static web service.

- Backend root directory: `backend`
- Frontend root directory: `frontend`
- Backend health check: `/api/ready`
- Frontend SPA rewrite: `/* -> /index.html`

Required environment variables:

- Backend: `MONGO_URI`, `JWT_SECRET`, `CLIENT_URL` (frontend URL), optional `GEMINI_API_KEY`, Twilio/Cloudinary keys
- Frontend: `VITE_API_URL` (set to `https://<your-backend>.onrender.com/api`)
