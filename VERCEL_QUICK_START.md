# Vercel Quick Start Guide

## 🚀 Deploy in 5 Minutes

### Step 1: Connect Repository to Vercel

1. Go to [vercel.com](https://vercel.com) and sign in
2. Click **"Add New"** → **"Project"**
3. Import your GitHub repository: `MariamSh03/chatio`
4. Authorize Vercel to access your GitHub account if prompted

### Step 2: Deploy Backend (Chat-Backend)

1. **Project Settings:**
   - Framework: **Next.js** (auto-detected)
   - Root Directory: **`Chat-Backend`**
   - Build Command: `npm run build` (default)
   - Output Directory: `.next` (default)

2. **Environment Variables:**
   Click "Environment Variables" and add:
   ```
   SUPABASE_URL=your_supabase_url
   SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
   GOOGLE_AI_API_KEY=your_google_ai_api_key
   ```

3. **Deploy:**
   - Click **"Deploy"**
   - Wait ~2-3 minutes
   - Copy the deployment URL (e.g., `https://chatio-backend-xxx.vercel.app`)

### Step 3: Deploy Frontend (Chat-UI)

1. **Create New Project:**
   - Click **"Add New"** → **"Project"** again
   - Import the same repository: `MariamSh03/chatio`

2. **Project Settings:**
   - Framework: **Vite** (or "Other")
   - Root Directory: **`Chat-UI`**
   - Build Command: `npm run build`
   - Output Directory: **`dist`**

3. **Environment Variables:**
   Add (replace with your actual backend URL from Step 2):
   ```
   VITE_API_BASE_URL=https://chatio-backend-xxx.vercel.app
   VITE_SUPABASE_URL=your_supabase_url
   VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
   ```

4. **Deploy:**
   - Click **"Deploy"**
   - Wait ~2-3 minutes
   - Your app is live! 🎉

### Step 4: Update CORS (Important!)

After both deployments, update CORS in your backend:

1. Go to your backend project in Vercel
2. Go to **Settings** → **Environment Variables**
3. Add:
   ```
   FRONTEND_URL=https://your-frontend-url.vercel.app
   ```

4. Update `Chat-Backend/lib/cors.ts`:
   ```typescript
   export const corsHeaders = {
     'Access-Control-Allow-Origin': process.env.FRONTEND_URL || '*',
     'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
     'Access-Control-Allow-Headers': 'Content-Type, Authorization',
   }
   ```

5. Redeploy the backend

## ✅ That's It!

Your ChatIO app is now live on Vercel!

- **Frontend**: `https://your-frontend-url.vercel.app`
- **Backend API**: `https://your-backend-url.vercel.app`

## 🔄 Automatic Deployments

Every push to `main` branch automatically deploys to production!

## 📝 Need Help?

See [VERCEL_DEPLOYMENT.md](./VERCEL_DEPLOYMENT.md) for detailed documentation.
