# Deploy AgriRent on Render.com

## Step 1: Connect GitHub to Render

1. Go to https://render.com
2. Sign up / Log in with GitHub
3. Click "New +" → "Web Service"
4. Select your GitHub repository (`agro_rent`)
5. Click "Connect"

---

## Step 2: Create Backend Service

### Configuration:
| Field | Value |
|-------|-------|
| **Name** | `agro-rent-api` |
| **Environment** | `Node` |
| **Build Command** | `npm install` |
| **Start Command** | `npm start` |
| **Root Directory** | `backend` |
| **Plan** | Free (or Pro) |

### Click Deploy

---

## Step 3: Add Backend Environment Variables

After backend deploys, click on the service and go to **Environment**:

| Key | Value |
|-----|-------|
| `NODE_ENV` | `production` |
| `MONGO_URI` | Your MongoDB connection string |
| `JWT_SECRET` | Random 32+ char string (keep secret) |
| `CLIENT_URL` | `https://agro-rent-frontend.onrender.com` |
| `CORS_ORIGINS` | `https://agro-rent-frontend.onrender.com` |
| `CLOUDINARY_CLOUD_NAME` | Your Cloudinary cloud name (optional) |
| `CLOUDINARY_API_KEY` | Your Cloudinary API key (optional) |
| `CLOUDINARY_API_SECRET` | Your Cloudinary API secret (optional) |

### Save & Deploy

---

## Step 4: Create Frontend Service

1. Click "New +" → "Static Site"
2. Connect same GitHub repository
3. Configuration:

| Field | Value |
|-------|-------|
| **Name** | `agro-rent-frontend` |
| **Build Command** | `npm install && npm run build` |
| **Publish Directory** | `frontend/dist` |
| **Root Directory** | `frontend` |

### Click Deploy

---

## Step 5: Add Frontend Environment Variables

After frontend deploys, go to **Environment**:

| Key | Value |
|-----|-------|
| `VITE_API_URL` | `https://agro-rent-api.onrender.com/api` |

### Save & Deploy

---

## Step 6: Connect Frontend to Backend (Rewrites)

Go to **Frontend service** → **Redirects/Rewrites**:

Add:
| Source | Destination | Permanent |
|--------|-------------|-----------|
| `/*` | `/index.html` | No |

This ensures SPA routing works.

---

## Step 7: Verify Deployment

### Check Backend:
```
https://agro-rent-api.onrender.com/api/ready
```
Should return `200 OK`

### Check Frontend:
```
https://agro-rent-frontend.onrender.com
```
Should load the app

### Test Login:
1. Click "Log in" on home page
2. Enter: `owner@demo.com` / `demo123`
3. Should see dashboard (no network error)

---

## If Deployment Fails

### Backend Build Error
```
❌ Error: npm install failed
```
**Fix**:
- Verify `backend/package.json` has no workspace dependencies
- Check `backend/src/server.js` exists
- Run locally: `cd backend && npm install`

### Frontend Build Error
```
❌ VITE_API_URL not defined
```
**Fix**:
- Go to Frontend → Environment
- Add `VITE_API_URL` = `https://agro-rent-api.onrender.com/api`
- Trigger redeploy

### Login Network Error
```
❌ Network Error: Backend unreachable
```
**Fix**:
- Check `CORS_ORIGINS` set in backend environment
- Verify `CLIENT_URL` set in backend environment
- Backend logs should show CORS rejection if incorrect

### Backend Takes Too Long to Start
- Render free tier goes to sleep
- Free tier database may be slow
- Upgrade to **Pro** for better performance

---

## Environment Variables Explained

### Backend
- **MONGO_URI**: MongoDB Atlas connection string (with password)
- **JWT_SECRET**: Generate with: `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`
- **CLIENT_URL**: Frontend URL for CORS
- **CORS_ORIGINS**: Comma-separated list of allowed origins

### Frontend
- **VITE_API_URL**: Backend API endpoint (without `/api` prefix, it's added in code)

---

## Auto-Deploy on Push

Both services are set to auto-deploy:
1. Push code to main branch
2. Render detects changes
3. Rebuilds and redeploys automatically
4. Check deployment logs in Render dashboard

---

## Quick Links

- **Render Dashboard**: https://dashboard.render.com
- **Backend Service Logs**: Dashboard → `agro-rent-api` → Logs
- **Frontend Service Logs**: Dashboard → `agro-rent-frontend` → Logs

---

## Delete a Service (if needed)

1. Go to service
2. Settings → Danger Zone
3. "Delete Web Service" or "Delete Static Site"

---

## Upgrade to Pro (Optional)

For production:
- Free tier sleeps after 15 min inactivity
- Pro tier: Always running, no cold starts
- Go to Service → Plan → Upgrade

---

## Troubleshooting

### Check Deployment Status
```
Render Dashboard → Service → Deployments
```
Shows build logs and any errors

### View Real-Time Logs
```
Render Dashboard → Service → Logs
```
Shows live server output

### Force Redeploy
```
Render Dashboard → Service → Manual Deploy
```
Rebuilds and redeploys without pushing code

### Test API Endpoint
```bash
curl https://agro-rent-api.onrender.com/api/ready
```

### Test with curl
```bash
curl -X POST https://agro-rent-api.onrender.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"owner@demo.com","password":"demo123"}'
```

---

## Next Steps

After deployment works:
1. ✅ Test all features (login, booking, equipment)
2. ✅ Configure custom domain (optional)
3. ✅ Set up monitoring/alerts
4. ✅ Backup MongoDB regularly
5. ✅ Monitor error logs in Render dashboard
