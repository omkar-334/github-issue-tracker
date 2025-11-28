# ✅ Tracker Integration Checklist

## Completed ✅

- [x] Frontend files in `tracker/` folder
- [x] Backend files in `tracker-backend/` folder (gitignored)
- [x] Tracker link added to all navigation menus
- [x] Error handling for missing backend repo
- [x] Setup guide created (`tracker-backend/SETUP.md`)
- [x] Workflows updated to use root paths (not `tracker/` subfolder)
- [x] Initial `repos.json` created in backend

## Remaining Tasks 🔧

### 1. Initialize Backend Repository (Required)

```bash
cd tracker-backend
git init
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git add .
git commit -m "Initial commit: GitHub Issue Tracker backend"
git branch -M main
git push -u origin main
```

### 2. Update Frontend Configuration (Required)

Edit `tracker/app.js`:

```javascript
const TRACKER_BACKEND_REPO = 'YOUR_USERNAME/YOUR_REPO_NAME'; // ⚠️ UPDATE THIS!
const TRACKER_BACKEND_BRANCH = 'main'; // Change if needed
```

### 3. Verify GitHub Actions (Required)

1. Go to backend repo → Actions tab
2. Verify workflows are visible
3. Trigger `monitor.yml` manually to test
4. Check workflow runs successfully

### 4. Add First Repository (Optional - Can do via UI)

Edit `repos.json` in backend repo or use frontend UI:

```json
{
  "repos": [
    {"name": "sktime/pytorch-forecasting", "last_issue_id": null}
  ]
}
```

### 5. Optional: Configure GitHub Token

If you want to add repos via frontend UI:

Edit `tracker/app.js`:
```javascript
const GITHUB_TOKEN = 'your_token_here'; // Optional
```

## Testing Checklist 🧪

- [ ] Backend repo exists and is accessible
- [ ] Frontend loads from backend repo (check browser console)
- [ ] Navigation link works on all pages
- [ ] Error message shows if backend not configured
- [ ] Workflows run successfully
- [ ] Issues appear after first monitor run

## File Structure Summary

```
website/
├── tracker/              ← Frontend (in website repo)
│   ├── index.html
│   ├── app.js           ← Configure TRACKER_BACKEND_REPO here!
│   ├── style.css
│   └── README.md
│
└── tracker-backend/      ← Backend (separate repo, gitignored)
    ├── .github/workflows/
    │   ├── monitor.yml
    │   └── update-repos.yml
    ├── repos.json
    ├── README.md
    └── SETUP.md         ← Setup instructions
```

## Quick Start

1. **Initialize backend repo** (see SETUP.md)
2. **Update `tracker/app.js`** with your backend repo name
3. **Add a repository** to monitor
4. **Wait 5 minutes** for first monitor run
5. **Visit** `yourwebsite.com/tracker`

Done! 🎉

