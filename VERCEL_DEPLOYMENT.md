# Vercel Deployment Guide

This guide explains how to deploy ChatIO to Vercel, including both the backend (Next.js) and frontend (Vite + React) applications.

## Prerequisites

1. **Vercel Account**: Sign up at [vercel.com](https://vercel.com)
2. **GitHub Repository**: Your code should be pushed to GitHub
3. **Environment Variables**: Prepare all required environment variables

## Project Structure

```
chatio/
├── Chat-Backend/     # Next.js API backend
├── Chat-UI/          # Vite + React frontend
└── vercel.json       # Root Vercel configuration
```

## Deployment Options

### Option 1: Deploy as Separate Projects (Recommended)

Deploy the backend and frontend as separate Vercel projects for better isolation and independent scaling.

#### Deploying Chat-Backend

1. **Import Project in Vercel:**
   - Go to [Vercel Dashboard](https://vercel.com/dashboard)
   - Click "Add New" → "Project"
   - Import your GitHub repository
   - Select the repository: `MariamSh03/chatio`

2. **Configure Project Settings:**
   - **Framework Preset**: Next.js (auto-detected)
   - **Root Directory**: `Chat-Backend`
   - **Build Command**: `npm run build` (default)
   - **Output Directory**: `.next` (default)
   - **Install Command**: `npm install` (default)

3. **Environment Variables:**
   Add the following environment variables in Vercel project settings:
   ```
   SUPABASE_URL=your_supabase_url
   SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
   GOOGLE_AI_API_KEY=your_google_ai_api_key
   NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
   ```

4. **Deploy:**
   - Click "Deploy"
   - Wait for build to complete
   - Note the deployment URL (e.g., `https://chatio-backend.vercel.app`)

#### Deploying Chat-UI

1. **Import Project in Vercel:**
   - Go to [Vercel Dashboard](https://vercel.com/dashboard)
   - Click "Add New" → "Project"
   - Import the same GitHub repository: `MariamSh03/chatio`

2. **Configure Project Settings:**
   - **Framework Preset**: Vite (or Other)
   - **Root Directory**: `Chat-UI`
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`

3. **Environment Variables:**
   Add the following environment variables:
   ```
   VITE_API_BASE_URL=https://your-backend-url.vercel.app
   VITE_SUPABASE_URL=your_supabase_url
   VITE_SUPABASE_ANON_KEY=your_anon_key
   ```

   **Note**: The frontend already uses `VITE_API_BASE_URL` environment variable (configured in `Chat-UI/src/lib/api.ts`).

4. **Deploy:**
   - Click "Deploy"
   - Wait for build to complete
   - Note the deployment URL (e.g., `https://chatio-ui.vercel.app`)

### Option 2: Deploy as Monorepo (Single Project)

Deploy both projects in a single Vercel project using the root `vercel.json` configuration.

1. **Import Project:**
   - Go to [Vercel Dashboard](https://vercel.com/dashboard)
   - Click "Add New" → "Project"
   - Import your GitHub repository

2. **Configure Project Settings:**
   - **Framework Preset**: Other
   - **Root Directory**: `/` (root)
   - **Build Command**: (handled by vercel.json)
   - **Output Directory**: (handled by vercel.json)

3. **Environment Variables:**
   Add all environment variables from both projects (prefix appropriately if needed)

4. **Deploy:**
   - Click "Deploy"
   - Both projects will build and deploy

## Environment Variables Reference

### Chat-Backend Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `SUPABASE_URL` | Your Supabase project URL | `https://xxx.supabase.co` |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key (for admin operations) | `eyJhbGc...` |
| `GOOGLE_AI_API_KEY` | Google AI API key for embeddings and Gemini | `AIza...` |
| `NEXT_PUBLIC_SUPABASE_URL` | Public Supabase URL (if needed) | `https://xxx.supabase.co` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Public Supabase anon key (if needed) | `eyJhbGc...` |

### Chat-UI Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `VITE_API_BASE_URL` | Backend API URL | `https://chatio-backend.vercel.app` |
| `VITE_SUPABASE_URL` | Supabase project URL | `https://xxx.supabase.co` |
| `VITE_SUPABASE_ANON_KEY` | Supabase anonymous key | `eyJhbGc...` |

## Post-Deployment Configuration

### 1. Update CORS Settings

After deploying the backend, update your CORS configuration in `Chat-Backend/lib/cors.ts` to include your frontend domain:

```typescript
export const corsHeaders = {
  'Access-Control-Allow-Origin': 'https://your-frontend-domain.vercel.app',
  'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
  'Access-Control-Allow-Headers': 'Content-Type, Authorization',
}
```

Or use environment variable:
```typescript
export const corsHeaders = {
  'Access-Control-Allow-Origin': process.env.FRONTEND_URL || '*',
  // ...
}
```

### 2. Update Frontend API URL

The frontend already uses the environment variable. Just set `VITE_API_BASE_URL` in Vercel:

```typescript
// Already configured in Chat-UI/src/lib/api.ts
const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'https://choricana-a5jy.vercel.app';
```

### 3. Database Setup

Make sure your Supabase database is configured:
- `pgvector` extension enabled
- `embedding` column exists in `messages` table
- RLS policies configured (if using)

## Continuous Deployment

Vercel automatically deploys on every push to your main branch. You can also:

- **Preview Deployments**: Every pull request gets a preview deployment
- **Production Deployments**: Pushes to main branch deploy to production
- **Manual Deployments**: Deploy specific commits from the dashboard

## Custom Domains

1. Go to your project settings in Vercel
2. Navigate to "Domains"
3. Add your custom domain
4. Follow DNS configuration instructions

## Troubleshooting

### Build Failures

1. **Check Build Logs**: View detailed logs in Vercel dashboard
2. **Verify Environment Variables**: Ensure all required variables are set
3. **Check Dependencies**: Ensure `package.json` has all required dependencies
4. **Node Version**: Vercel uses Node 18.x by default (can be changed in settings)

### API Not Working

1. **Check CORS**: Ensure frontend URL is allowed in CORS headers
2. **Verify API URL**: Check that frontend is pointing to correct backend URL
3. **Check Environment Variables**: Ensure all API keys are set correctly

### Frontend Not Loading

1. **Check Build Output**: Verify `dist` directory is being created
2. **Check Routes**: Ensure `vercel.json` rewrites are correct
3. **Check Environment Variables**: Verify `VITE_API_BASE_URL` is set

## Monitoring

- **Analytics**: Enable Vercel Analytics in project settings
- **Logs**: View real-time logs in Vercel dashboard
- **Performance**: Monitor Core Web Vitals in Analytics

## Additional Resources

- [Vercel Documentation](https://vercel.com/docs)
- [Next.js on Vercel](https://vercel.com/docs/frameworks/nextjs)
- [Vite on Vercel](https://vercel.com/docs/frameworks/vite)
- [Environment Variables](https://vercel.com/docs/environment-variables)

## Support

For issues specific to ChatIO deployment, check:
- Build logs in Vercel dashboard
- GitHub Issues: [https://github.com/MariamSh03/chatio/issues](https://github.com/MariamSh03/chatio/issues)
