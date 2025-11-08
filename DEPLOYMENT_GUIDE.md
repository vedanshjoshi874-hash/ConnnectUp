# ConnectUp Deployment Guide

## 🚀 Quick Deploy to Render

This project is configured to deploy both frontend and backend to Render using the `render.yaml` blueprint.

### Prerequisites
1. A Render account (https://render.com)
2. A MongoDB Atlas database (https://www.mongodb.com/cloud/atlas)
3. Your GitHub repository connected to Render

### Step 1: Set Up MongoDB Atlas
1. Create a free MongoDB Atlas cluster
2. Create a database user with password
3. Whitelist all IPs (0.0.0.0/0) for Render access
4. Copy your connection string

### Step 2: Deploy to Render

#### Option A: Using render.yaml (Recommended)
1. Go to Render Dashboard
2. Click "New" → "Blueprint"
3. Connect your GitHub repository
4. Render will detect `render.yaml` and create both services
5. Add your `MONGODB_URI` in the backend service environment variables

#### Option B: Manual Deployment

**Backend:**
1. New → Web Service
2. Connect repository
3. Settings:
   - **Name:** connectup-backend
   - **Root Directory:** backend
   - **Environment:** Node
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
   - **Environment Variables:**
     ```
     NODE_ENV=production
     MONGODB_URI=<your-mongodb-connection-string>
     JWT_SECRET=<generate-a-secure-random-string>
     JWT_EXPIRES_IN=90d
     FRONTEND_URL=https://connectup-frontend.onrender.com
     PORT=5000
     ```

**Frontend:**
1. New → Static Site
2. Connect repository
3. Settings:
   - **Name:** connectup-frontend
   - **Root Directory:** frontend
   - **Build Command:** `npm install && npm run build`
   - **Publish Directory:** `dist`
   - **Environment Variables:**
     ```
     VITE_API_URL=https://connectup-backend.onrender.com/api/v1
     ```

### Step 3: Update CORS After Deployment
After both services are deployed, update the backend's `FRONTEND_URL` environment variable with your actual frontend URL.

### Step 4: Test Your Deployment
1. Visit your frontend URL
2. Try registering a new user
3. Check browser console for any API errors
4. Check backend logs in Render dashboard

## 🔧 Troubleshooting

### Issue: API calls fail with CORS errors
**Solution:** Ensure `FRONTEND_URL` in backend matches your frontend URL exactly (including https://)

### Issue: 404 on page refresh
**Solution:** Already fixed with `_redirects` file in `frontend/public/`

### Issue: Environment variables not loading
**Solution:** 
- Frontend: Variables must start with `VITE_`
- Backend: Restart service after adding variables

### Issue: Database connection fails
**Solution:**
- Check MongoDB Atlas IP whitelist includes 0.0.0.0/0
- Verify connection string format
- Check database user permissions

## 📱 Local Development

### Backend
```bash
cd backend
npm install
# Create .env file with your MongoDB URI
npm run dev
```

### Frontend
```bash
cd frontend
npm install
npm run dev
```

## 🔐 Security Notes

1. **Never commit `.env` files** - They contain sensitive credentials
2. **Use strong JWT secrets** - Generate with: `openssl rand -base64 32`
3. **Rotate secrets regularly** - Especially after team member changes
4. **Monitor API usage** - Check Render logs for suspicious activity

## 📊 Monitoring

- **Backend Logs:** Render Dashboard → connectup-backend → Logs
- **Frontend Logs:** Browser Console (F12)
- **Database:** MongoDB Atlas → Metrics

## 🔄 Continuous Deployment

Both services auto-deploy when you push to the `main` branch:
```bash
git add .
git commit -m "Your changes"
git push origin main
```

Render will automatically:
1. Pull latest code
2. Run build commands
3. Deploy new version
4. Zero-downtime deployment

## 📞 Support

If you encounter issues:
1. Check Render logs
2. Check browser console
3. Verify environment variables
4. Check MongoDB Atlas connection
