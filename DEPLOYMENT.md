# Deployment Guide

This guide covers deploying the Retail Sales Management System with:
- **Frontend**: Vercel
- **Backend**: Render.com

## Prerequisites

- GitHub account
- Vercel account (sign up at [vercel.com](https://vercel.com))
- Render.com account (sign up at [render.com](https://render.com))
- Git installed locally

---

## Part 1: GitHub Repository Setup

### 1. Update Git Remote (if needed)

The repository currently points to `https://github.com/prasomjain/Retailo.git`. You'll want to update this to your own GitHub repository.

```bash
# Remove the old remote
git remote remove origin

# Add your new remote (replace with your GitHub username)
git remote add origin https://github.com/YOUR_USERNAME/Retailo.git
```

### 2. Commit Your Changes

```bash
# Stage the changes
git add .

# Commit with a message
git commit -m "Update author information and prepare for deployment"

# Push to GitHub
git push -u origin master
```

If you haven't created the repository on GitHub yet:
1. Go to [github.com](https://github.com)
2. Click "New Repository"
3. Name it "Retailo" (or your preferred name)
4. **Do NOT initialize with README** (since you already have one)
5. Click "Create Repository"
6. Follow the instructions to push an existing repository

---

## Part 2: Backend Deployment on Render.com

### 1. Create a New Web Service

1. Go to [render.com](https://render.com) and sign in
2. Click "New +" → "Web Service"
3. Connect your GitHub repository
4. Select the "Retailo" repository

### 2. Configure the Web Service

Use these settings:

- **Name**: `retailo-backend` (or your preferred name)
- **Region**: Choose closest to your users
- **Branch**: `master`
- **Root Directory**: `backend`
- **Runtime**: `Node`
- **Build Command**: `npm install`
- **Start Command**: `npm start`
- **Instance Type**: Free (or your preferred tier)

### 3. Environment Variables

Add these environment variables in Render:

- `PORT`: `3001` (Render will override this, but good to have)
- `NODE_ENV`: `production`

### 4. Important Notes for Render

> **⚠️ Database Consideration**: The backend uses SQLite with a file-based database (`sales.db`). On Render's free tier, the filesystem is ephemeral, meaning the database will be reset on each deployment.

**Solutions:**
- **Option A (Recommended)**: Migrate to PostgreSQL (Render offers free PostgreSQL)
- **Option B**: Keep SQLite but understand data will reset on redeploys
- **Option C**: Use Render's persistent disk (paid feature)

### 5. Deploy

1. Click "Create Web Service"
2. Wait for the build to complete
3. Note your backend URL (e.g., `https://retailo-backend.onrender.com`)

### 6. Test the Backend

Visit: `https://YOUR_BACKEND_URL.onrender.com/health`

You should see: `{"status":"ok"}`

---

## Part 3: Frontend Deployment on Vercel

### 1. Update Vercel Configuration

The `frontend/vercel.json` file needs to be updated with your actual Render backend URL.

**Current configuration** (already points to Render):
```json
{
    "rewrites": [
        {
            "source": "/api/:path*",
            "destination": "https://retailo.onrender.com/api/:path*"
        },
        {
            "source": "/(.*)",
            "destination": "/index.html"
        }
    ]
}
```

**Update line 5** with your actual Render backend URL:
```json
"destination": "https://YOUR_BACKEND_URL.onrender.com/api/:path*"
```

### 2. Deploy to Vercel

#### Option A: Using Vercel CLI

```bash
# Install Vercel CLI globally
npm install -g vercel

# Navigate to frontend directory
cd frontend

# Deploy
vercel

# Follow the prompts:
# - Set up and deploy? Yes
# - Which scope? Your account
# - Link to existing project? No
# - Project name? retailo (or your choice)
# - Directory? ./ (current directory)
# - Override settings? No

# For production deployment
vercel --prod
```

#### Option B: Using Vercel Dashboard

1. Go to [vercel.com](https://vercel.com) and sign in
2. Click "Add New" → "Project"
3. Import your GitHub repository
4. Configure the project:
   - **Framework Preset**: Vite
   - **Root Directory**: `frontend`
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`
5. Click "Deploy"

### 3. Verify Deployment

Once deployed, Vercel will give you a URL like:
`https://retailo-YOUR_USERNAME.vercel.app`

Visit the URL and test:
- The app loads correctly
- Search functionality works
- Filters work
- Data is being fetched from your Render backend

---

## Part 4: Post-Deployment Checklist

### ✅ Backend (Render)
- [ ] Backend is deployed and running
- [ ] Health check endpoint works (`/health`)
- [ ] API endpoints are accessible (`/api/sales`)
- [ ] Database is populated with data
- [ ] CORS is configured correctly

### ✅ Frontend (Vercel)
- [ ] Frontend is deployed and accessible
- [ ] API calls are reaching the backend
- [ ] All features work (search, filter, pagination)
- [ ] UI displays correctly
- [ ] No console errors

### ✅ GitHub
- [ ] Repository is updated with latest changes
- [ ] Sensitive files are in `.gitignore`
- [ ] README is updated with deployment URLs

---

## Troubleshooting

### Frontend can't connect to backend

1. Check the backend URL in `frontend/vercel.json`
2. Verify the backend is running on Render
3. Check browser console for CORS errors
4. Ensure backend CORS allows your Vercel domain

### Backend database is empty

1. The SQLite database needs to be initialized with data
2. Consider migrating to PostgreSQL for persistent data
3. Or implement a data seeding script that runs on startup

### Vercel build fails

1. Check that all dependencies are in `package.json`
2. Verify build command is correct
3. Check Vercel build logs for specific errors

---

## Environment URLs

After deployment, update these in your README:

- **Frontend (Vercel)**: `https://your-app.vercel.app`
- **Backend (Render)**: `https://your-backend.onrender.com`
- **GitHub Repository**: `https://github.com/YOUR_USERNAME/Retailo`

---

## Future Improvements

1. **Database Migration**: Move from SQLite to PostgreSQL
2. **Environment Variables**: Use Vercel environment variables for backend URL
3. **CI/CD**: Set up automatic deployments on push
4. **Monitoring**: Add error tracking (Sentry, LogRocket)
5. **Analytics**: Add usage analytics (Google Analytics, Plausible)

---

## Support

For issues:
1. Check Vercel deployment logs
2. Check Render deployment logs
3. Review browser console errors
4. Verify API endpoints manually
