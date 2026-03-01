# Environment Variables Setup

This document lists all environment variables needed for deployment and local development.

## Vercel Production Environment Variables

Set these in your Vercel Dashboard → Project → Settings → Environment Variables:

### Required (Backend API)

| Variable | Value | Description |
|----------|-------|-------------|
| `MONGODB_URI` | `mongodb+srv://tushsood_db_user:I75ONCBbAiF0hqA4@cluster0.besf6nc.mongodb.net/?appName=Cluster0` | MongoDB Atlas connection string |
| `MONGODB_DB_NAME` | `arxive_intelli` | Database name in your MongoDB cluster |
| `MONGODB_COLLECTION` | `papers` | Collection name containing paper documents |

### Optional (Backend API)

| Variable | Value | Description |
|----------|-------|-------------|
| `BM25_URL` | (optional) | URL to prebuilt BM25 index for faster searches |

### Frontend Variables

**DO NOT SET `REACT_APP_API_URL` in Vercel production!**

The frontend and backend are deployed together on the same Vercel app. The frontend automatically uses same-origin `/api/*` routes. Setting `REACT_APP_API_URL` will cause CORS errors.

---

## Local Development Environment Variables

### Backend (API)

Create `api/.env` (not tracked by git):

```env
MONGODB_URI=mongodb+srv://tushsood_db_user:I75ONCBbAiF0hqA4@cluster0.besf6nc.mongodb.net/?appName=Cluster0
MONGODB_DB_NAME=arxive_intelli
MONGODB_COLLECTION=papers
```

Or set them in your shell:
```bash
# PowerShell
$env:MONGODB_URI="mongodb+srv://tushsood_db_user:I75ONCBbAiF0hqA4@cluster0.besf6nc.mongodb.net/?appName=Cluster0"
$env:MONGODB_DB_NAME="arxive_intelli"
$env:MONGODB_COLLECTION="papers"
```

### Frontend

The file `frontend/.env.local` is already configured:
```env
REACT_APP_API_URL=http://localhost:8001
```

This tells the React app to call your local API during development.

---

## MongoDB Atlas Network Access

**IMPORTANT:** For Vercel to connect to MongoDB Atlas, you need to allow Vercel's IP addresses in MongoDB Atlas:

1. Go to MongoDB Atlas → Network Access
2. Click **"Add IP Address"**
3. Add **0.0.0.0/0** (allow all IPs) for simplicity
   - OR add specific Vercel IPs if you want tighter security
4. Click **"Confirm"**

Without this, Vercel will get connection timeouts when trying to reach MongoDB.

---

## Summary: What to Set in Vercel

```
MONGODB_URI = mongodb+srv://tushsood_db_user:I75ONCBbAiF0hqA4@cluster0.besf6nc.mongodb.net/?appName=Cluster0
MONGODB_DB_NAME = arxive_intelli
MONGODB_COLLECTION = papers
```

**Do NOT set:**
- `REACT_APP_API_URL` (causes CORS errors)
- `PAPERS_DATA_URL` (obsolete JSON-based system)

---

## Verification

After setting environment variables and deploying:

1. Visit `https://your-app.vercel.app/api/health`
2. You should see:
   ```json
   {
     "status": "ok",
     "papers_loaded": <your_count>,
     "bm25_loaded": false,
     "mongodb_connected": true,
     "mongodb_db": "arxive_intelli",
     "mongodb_collection": "papers"
   }
   ```

If `mongodb_connected` is `false` or `papers_loaded` is `0`, check:
- MongoDB Atlas Network Access settings
- Environment variables are set correctly in Vercel
- Vercel function logs for error messages
