# 🔍 GitHub Issue Tracker - Complete Setup Guide

This is the **backend repository** for the GitHub Issue Tracker. This guide covers both backend setup and frontend configuration.

## 📋 Architecture Overview

```
┌─────────────────────────────────┐
│   Frontend (Website Repo)    │
│   tracker/                      │
│   - HTML/CSS/JS                 │
│   - Loads data from backend     │
└──────────────┬──────────────────┘
               │
               │ (GitHub Raw URLs)
               ▼
┌─────────────────────────────────┐
│   Backend (This Repo)          │
│   - GitHub Actions workflows    │
│   - repos.json                  │
│   - issues/ (JSON files)        │
└─────────────────────────────────┘
```

## 📁 Repository Structure

```
tracker-backend/
├── .github/
│   └── workflows/
│       └── monitor.yml      # Monitors issues every 10 minutes
├── repos.json               # List of monitored repositories
├── issues/                  # Issue data (auto-generated)
│   ├── index.json
│   ├── 2025-11.json
│   └── ...
└── README.md               # This file
```

**Frontend Structure** (in separate website repo):
```
tracker/                    ← Frontend folder (in website repo)
├── index.html              # Main dashboard
├── app.js                  # Frontend logic (CONFIGURE BACKEND REPO HERE!)
├── style.css               # Styling
└── README.md               # (can be removed, info merged here)
```

## ⚠️ Important Notes

**This repository should NOT contain:**
- ❌ HTML files
- ❌ JavaScript files
- ❌ CSS files
- ❌ Frontend code

**This repository contains ONLY:**
- ✅ GitHub Actions workflows
- ✅ `repos.json` (configuration)
- ✅ `issues/` folder (data storage, auto-generated)

The frontend is in a separate repository (website repo, `tracker/` folder).

---

## 🚀 Setup Instructions

### Step 1: Create GitHub Repository

1. Go to GitHub and create a new repository (e.g., `github-issue-tracker`)
2. Make it **public** (so GitHub Raw URLs work) or **private** (if you prefer, but frontend needs access)
3. **Don't** initialize with README, .gitignore, or license (we'll add our own)

### Step 2: Initialize Local Repository

```bash
cd tracker-backend
git init
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
```

### Step 3: Initial Commit

```bash
# Add all files
git add .

# Commit
git commit -m "Initial commit: GitHub Issue Tracker backend"

# Push to main branch
git branch -M main
git push -u origin main
```

### Step 4: Configure Frontend

Edit `tracker/app.js` in your website repo:

```javascript
const TRACKER_BACKEND_REPO = 'YOUR_USERNAME/YOUR_REPO_NAME'; // Update this!
const TRACKER_BACKEND_BRANCH = 'main'; // Change if using different branch
```

### Step 5: Verify Setup

1. Check that `repos.json` exists in the backend repo
2. Check that `.github/workflows/` folder exists with workflows
3. Go to your backend repo → Actions tab
4. Verify workflows are running (or trigger manually)

### Step 6: Add First Repository

Edit `repos.json` in the backend repo:

```json
{
  "repos": [
    {"name": "owner/repo", "last_issue_id": null}
  ]
}
```

Or use the frontend UI (if `GITHUB_TOKEN` is configured in `tracker/app.js`).

---

## 📊 How It Works

### Backend Monitoring

1. **First Run**: When a repository is added (no `last_issue_id`), the workflow:
   - Fetches **ALL issues** from the repository by paginating through all pages
   - Indexes every issue for complete historical data
   - Sets `last_issue_id` to the highest issue ID found

2. **Incremental Updates**: After the first run, the workflow:
   - Runs every **10 minutes** automatically
   - Only fetches issues created in the last 30 days (to catch any missed updates)
   - Only adds issues with ID > `last_issue_id` (new issues)
   - Updates `last_issue_id` to track progress

3. **Data Storage**:
   - Issues stored in `issues/` folder (paginated by month: `YYYY-MM.json`)
   - `issues/index.json` contains all active issues for quick frontend loading
   - Old issues (>1 year) are archived automatically

### Frontend Loading

1. **Frontend loads data** from backend repo via GitHub Raw URLs:
   - `https://raw.githubusercontent.com/{BACKEND_REPO}/{BRANCH}/issues/index.json`
   - `https://raw.githubusercontent.com/{BACKEND_REPO}/{BRANCH}/repos.json`

2. **Auto-refresh**: Frontend refreshes data every 10 minutes to match backend schedule

3. **Adding repositories**:
   - **With Token**: Frontend can directly update `repos.json` via GitHub API
   - **Without Token**: Shows instructions to manually edit `repos.json` in backend repo

---

## ⚙️ Configuration

### Backend Configuration

**Initial `repos.json`**:
```json
{
  "repos": []
}
```

**GitHub Actions Workflows**:
- `monitor.yml` - Runs every 10 minutes, monitors issues (indexes all on first run)

**GitHub Secrets** (optional, for email notifications):
- `EMAIL_TO` - Your email address
- `SMTP_HOST` - SMTP server (e.g., `smtp.gmail.com`)
- `SMTP_PORT` - SMTP port (e.g., `587`)
- `SMTP_USER` - SMTP username
- `SMTP_PASS` - SMTP password or app-specific password

### Frontend Configuration

**Required**: Update `tracker/app.js` with your backend repository:

```javascript
const TRACKER_BACKEND_REPO = 'YOUR_USERNAME/YOUR_BACKEND_REPO'; // Change this!
const TRACKER_BACKEND_BRANCH = 'main'; // Change if using different branch
```

**Optional**: GitHub Token for Adding Repos

If you want to add/remove repos directly from the frontend, add a GitHub Personal Access Token:

```javascript
const GITHUB_TOKEN = 'your_token_here'; // Optional
```

**Token Permissions Needed:**
- `repo` (Full control of private repositories)
- `workflow` (Update GitHub Action workflows)

---

## ✏️ Adding Repositories

### Method 1: Via Frontend (Requires Token)

If `GITHUB_TOKEN` is set in `tracker/app.js`:
1. Visit the tracker page (e.g., `yourwebsite.com/tracker/`)
2. Enter repository (URL or `owner/repo` format)
3. Click "Add"
4. Repository is automatically added to backend
5. First monitor run will index all issues

### Method 2: Manual Edit (No Token Needed)

1. Go to your backend repository
2. Edit `repos.json`
3. Add the repository to the `repos` array:
   ```json
   {
     "repos": [
       {"name": "owner/repo", "last_issue_id": null}
     ]
   }
   ```
4. Commit the change
5. Backend monitor will pick it up automatically on next run (or trigger manually)

### Repository Input Format

The frontend accepts both formats:
- **GitHub URL**: `https://github.com/owner/repo` or `github.com/owner/repo`
- **Owner/Repo**: `owner/repo`

Both are automatically normalized to `owner/repo` format.

---

## 🔗 Frontend Integration

The frontend loads data from this repository using GitHub Raw URLs:
- `https://raw.githubusercontent.com/{THIS_REPO}/main/issues/index.json`
- `https://raw.githubusercontent.com/{THIS_REPO}/main/repos.json`

**Important**: Make sure the frontend's `tracker/app.js` has the correct `TRACKER_BACKEND_REPO` configured.

### Frontend Deployment

The frontend works as static files. Deploy to:
- GitHub Pages
- Netlify
- Vercel
- Any static hosting

**Note**: The tracker is accessible via direct URL only (not in navigation menu):
- `yourwebsite.com/tracker/` or `yourwebsite.com/tracker/index.html`

---

## ✅ Verification Checklist

- [ ] Backend repository created on GitHub
- [ ] Files pushed to GitHub
- [ ] `TRACKER_BACKEND_REPO` configured in `tracker/app.js`
- [ ] GitHub Actions workflows visible in backend repo
- [ ] First repository added to `repos.json`
- [ ] Monitor workflow runs successfully
- [ ] Frontend loads data from backend repo
- [ ] Issues appear after first monitor run

---

## 🔧 Troubleshooting

### Workflows Not Running
- Check repository settings → Actions → Allow all actions
- Verify workflows are in `.github/workflows/` folder
- Check workflow syntax in Actions tab
- Verify cron schedule is correct (`*/10 * * * *` for every 10 minutes)

### Frontend Can't Load Data
- Verify backend repo is public (or frontend has access)
- Check `TRACKER_BACKEND_REPO` is correct in `tracker/app.js`
- Verify branch name matches (`main` vs `master`)
- Check browser console for CORS or 404 errors
- Ensure `repos.json` and `issues/index.json` exist in backend repo

### No Issues Showing
- Wait 10 minutes for first monitor run (or trigger manually)
- Check workflow logs in Actions tab for errors
- Verify repositories in `repos.json` are correct format
- Ensure repositories are public or token has access
- Check that `last_issue_id` is being updated in `repos.json`

### First Run Taking Too Long
- First run indexes ALL issues, which can take time for large repositories
- Check workflow logs to see progress
- Large repos may take multiple workflow runs to complete
- Monitor will continue from where it left off

### Email Notifications Not Working
- Verify all email secrets are set correctly
- Check workflow logs for SMTP connection errors
- Ensure you're using an app-specific password for Gmail
- Verify `EMAIL_TO` secret is set (workflow checks this)

---

## 📝 Next Steps

1. Add repositories to monitor (edit `repos.json` or use UI)
2. Wait for first monitor run (10 minutes) or trigger manually
3. Visit your website's tracker page (`yourwebsite.com/tracker/`)
4. Issues should appear automatically!

---

## 🎯 Key Features

- ✅ **Complete Historical Data**: First run indexes ALL issues
- ✅ **Efficient Updates**: Subsequent runs only fetch new issues
- ✅ **Automatic Archiving**: Issues older than 1 year are archived
- ✅ **PR Detection**: Automatically detects linked pull requests
- ✅ **Paginated Storage**: Issues stored by month for efficient loading
- ✅ **Real-time Frontend**: Auto-refreshes every 10 minutes
- ✅ **No Backend Server**: Everything runs on GitHub Actions + GitHub Pages

---

## 📚 Additional Resources

- Frontend code: `tracker/` folder in website repository
- Backend workflows: `.github/workflows/` in this repository
- Data files: `repos.json` and `issues/` folder
- Configuration: Edit `tracker/app.js` for frontend, `repos.json` for backend
