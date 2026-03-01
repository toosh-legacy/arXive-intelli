# Quick Deploy Guide - MongoDB Atlas + Vercel

Deploy your arXiv search engine on Vercel with MongoDB Atlas as the data backend.

## Prerequisites

- MongoDB Atlas cluster with papers data loaded
- Vercel account
- GitHub repository

## Deployment Steps

## Step 1: Set Vercel Environment Variables

1. Go to [Vercel Dashboard](https://vercel.com/dashboard)
2. Select your project
3. Go to **Settings** → **Environment Variables**

**Important:** The frontend and API are both on the same Vercel app. The frontend calls `/api/*` on your Vercel URL (same origin). **Do NOT set `REACT_APP_API_URL`** — and **delete it if it exists** (e.g. `https://your-backend-api.com`). That placeholder causes CORS errors and "Failed to fetch".

4. Add these MongoDB variables:
   - **Key**: `MONGODB_URI`
   - **Value**: Your MongoDB Atlas connection string (e.g., `mongodb+srv://user:pass@cluster0.xxxxx.mongodb.net/?appName=Cluster0`)
   - **Environment**: Production (and Preview if you want)
   
   - **Key**: `MONGODB_DB_NAME`
   - **Value**: Your database name (e.g., `arxive_intelli`)
   - **Environment**: Production (and Preview if you want)
   
   - **Key**: `MONGODB_COLLECTION`
   - **Value**: Your collection name (e.g., `papers`)
   - **Environment**: Production (and Preview if you want)

5. Click **"Save"**
6. **Remove** `REACT_APP_API_URL` if present (⋯ → Delete).

## Step 2: Deploy to Vercel

### If you haven't connected to Vercel yet:

1. Go to [Vercel](https://vercel.com)
2. Click **"Add New Project"**
3. Import your GitHub repository
4. Vercel will auto-detect the configuration
5. Click **"Deploy"**

### If already connected:

Just push your code:
```bash
git add .
git commit -m "Migrate to MongoDB Atlas backend"
git push
```

Vercel will automatically redeploy.

## Step 3: Verify It Works

1. Wait for deployment to complete (2-3 minutes)
2. Visit your Vercel URL: `https://your-app.vercel.app`
3. Check health endpoint: `https://your-app.vercel.app/api/health`

You should see:
```json
{
  "status": "ok",
  "papers_loaded": 80000,
  "bm25_loaded": false,
  "mongodb_connected": true,
  "mongodb_db": "arxive_intelli",
  "mongodb_collection": "papers"
}
```

4. Try a search! It should now search through all your papers.

## Troubleshooting

### CORS / "Failed to fetch" / "your-backend-api.com"

You see `Access to fetch at 'https://your-backend-api.com/api/...' has been blocked by CORS policy`.

- **Cause:** `REACT_APP_API_URL` is set to a placeholder like `https://your-backend-api.com`. The real API lives on your Vercel app at `https://your-app.vercel.app/api/...`.
- **Fix:** In Vercel → Project → **Settings** → **Environment Variables**, **delete** `REACT_APP_API_URL`. Redeploy. The frontend will use same-origin (`/api/...`) and CORS goes away.

### "papers_loaded: 0"
- Check Vercel function logs (Dashboard → Your Project → Functions → View Logs)
- Verify `MONGODB_URI`, `MONGODB_DB_NAME`, and `MONGODB_COLLECTION` are set correctly
- Test MongoDB connection from your local machine using the same URI
- Check MongoDB Atlas network access allows Vercel IPs (or allow all IPs: 0.0.0.0/0)

### "Failed to load papers from MongoDB"
- Make sure MongoDB Atlas cluster is running
- Verify database user has read permissions
- Check network access settings in Atlas
- Confirm collection name and database name match your setup

### MongoDB connection timeout
- Normal for first request (~5-10 seconds to establish connection)
- Check MongoDB Atlas status page
- Verify connection string format is correct

### Memory errors
- Vercel Hobby plan: 1024MB memory limit
- If you hit limits, consider:
  - Using Vercel Pro (3008MB)
  - Or reduce the number of papers exported

## What Happens Behind the Scenes

1. **First Request**: API connects to MongoDB Atlas using `MONGODB_URI`
2. **Authentication**: Authenticates with database user credentials
3. **Data Fetch**: Retrieves all papers from specified collection
4. **Normalization**: Papers are loaded into memory and indexed
5. **Ready**: Search engine is ready to use!

Each serverless function invocation fetches data from MongoDB (Vercel caches function instances for ~5 minutes).

## Next Steps (Optional)

- **Optimize BM25 Index**: Precompute BM25 index for faster searches
- **Monitor Performance**: Check Vercel Analytics for usage stats
- **Set Custom Domain**: Add your own domain in Vercel settings
- **MongoDB Indexes**: Add indexes on common search fields (category, published date)

---

**That's it!** Your search engine should now be running with MongoDB Atlas! 🚀
