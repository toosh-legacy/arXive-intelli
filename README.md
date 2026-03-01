# arXiv-Intelli - Research Paper Search Engine

A full-featured search engine for academic research papers from arXiv, deployed on Vercel with support for 80,000+ papers from MongoDB Atlas.

## ✨ Features

- 🔍 **Full-text BM25 Search** - Fast and relevant results
- 🎯 **Advanced Filtering** - Filter by category, year, author
- 📊 **Statistics Dashboard** - Overview of your paper collection
- 🎨 **Modern Web UI** - Clean, responsive React interface
- 🚀 **Vercel Deployment** - Serverless deployment with MongoDB Atlas
- ⚡ **Fast & Scalable** - Handles 80k+ papers efficiently

## 🚀 Quick Deploy to Vercel

See **[QUICK_DEPLOY.md](QUICK_DEPLOY.md)** for step-by-step deployment instructions and **[ENV_SETUP.md](ENV_SETUP.md)** for environment variables.

### Quick Summary:

1. **Create MongoDB Atlas cluster** and import your `papers` data
2. **Set Vercel env vars**: `MONGODB_URI`, `MONGODB_DB_NAME`, `MONGODB_COLLECTION`
3. **Configure MongoDB Atlas Network Access**: Allow Vercel IPs (or 0.0.0.0/0)
4. **Deploy**: Push to GitHub and Vercel auto-deploys!

## 📁 Project Structure

```
├── api/
│   ├── index.py          # FastAPI serverless function
│   ├── requirements.txt  # Python dependencies
│   └── papers_data.json  # Legacy local data file (optional)
├── frontend/
│   └── src/              # React frontend
├── vercel.json           # Vercel configuration
└── QUICK_DEPLOY.md       # Deployment guide
```

## 🔧 Local Development

### Prerequisites
- Python 3.8+
- Node.js 16+

### Backend
```bash
cd api
pip install -r requirements.txt
# Required env vars:
# MONGODB_URI=mongodb+srv://...
# MONGODB_DB_NAME=arxive_intelli
# MONGODB_COLLECTION=papers
```

### Frontend
```bash
cd frontend
npm install
npm start
```

## 📚 Documentation

- **[ENV_SETUP.md](ENV_SETUP.md)** - Complete environment variables guide
- **[QUICK_DEPLOY.md](QUICK_DEPLOY.md)** - Complete deployment guide
- **[DEBUG.md](DEBUG.md)** - Troubleshooting guide
- **API Routes**: `/api/search`, `/api/stats`, `/api/facets`, `/api/health`

## 🗄️ MongoDB Atlas Setup

Your MongoDB collection should have documents with these fields:

```json
{
  "id": "unique_paper_id",
  "title": "Paper Title",
  "abstract": "Abstract text...",
  "authors": "Author 1, Author 2",
  "categories": "cs.AI cs.LG",
  "category": "cs.AI",
  "published": "2024-01-15",
  "updated": "2024-01-20",
  "url": "https://arxiv.org/abs/...",
  "pdf_url": "https://arxiv.org/pdf/..."
}
```

**Alternative field names** (for compatibility):
- `paper_id` or `paperId` instead of `id`
- `primary_category` instead of `category`

The API automatically normalizes these variants.

## 🛠️ Technologies

- **FastAPI** - Python web framework
- **React** - Frontend framework
- **BM25 (rank-bm25)** - Search ranking algorithm
- **Vercel** - Serverless hosting platform
