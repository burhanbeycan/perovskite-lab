# 🚀 Deployment Guide - PerovskiteLab

This guide will help you deploy PerovskiteLab to GitHub Pages in **under 5 minutes**.

---

## ✅ Prerequisites

- GitHub account
- Git installed on your computer (optional, can use GitHub web interface)

---

## ⚙️ GitHub Actions (Automatic Deployment)

This repository includes GitHub Actions workflows for **automatic deployment**.

### Setup

1. **Push your code** to GitHub
2. Go to **Settings** → **Pages**
3. Under **Source**, select **GitHub Actions**
4. Push to `main` branch triggers automatic deployment

See [.github/GITHUB_ACTIONS.md](.github/GITHUB_ACTIONS.md) for detailed instructions.

---

## 🎯 Quick Deploy (Recommended)

### Method 1: GitHub Web Interface (Easiest)

1. **Create a new repository** on GitHub:
   - Go to https://github.com/new
   - Repository name: `perovskite-lab`
   - Description: "Autonomous ML-Driven Perovskite Synthesis Platform"
   - Public repository
   - Click "Create repository"

2. **Upload the file**:
   - Click "uploading an existing file"
   - Drag and drop `index.html`
   - Commit message: "Initial commit: PerovskiteLab"
   - Click "Commit changes"

3. **Enable GitHub Pages**:
   - Go to **Settings** → **Pages**
   - Source: **Deploy from a branch**
   - Branch: **main** / **root**
   - Click **Save**

4. **Wait 1-2 minutes** for deployment

5. **Access your site**:
   ```
   https://YOUR_USERNAME.github.io/perovskite-lab/
   ```

**Done! 🎉**

---

### Method 2: Git Command Line

```bash
# 1. Create repository on GitHub first (https://github.com/new)

# 2. Clone and setup
git clone https://github.com/YOUR_USERNAME/perovskite-lab.git
cd perovskite-lab

# 3. Copy the index.html file to this directory

# 4. Commit and push
git add index.html README.md LICENSE
git commit -m "Initial commit: PerovskiteLab"
git push origin main

# 5. Enable GitHub Pages in repository settings

# 6. Access at: https://YOUR_USERNAME.github.io/perovskite-lab/
```

---

## 🎨 Customization

### Update Personal Information

Before deploying, replace placeholders in `README.md`:

```bash
# Replace YOUR_USERNAME with your GitHub username
sed -i 's/YOUR_USERNAME/your-github-username/g' README.md

# Replace Your Name
sed -i 's/Your Name/John Doe/g' README.md

# Replace email
sed -i 's/your.email@university.edu/john.doe@mit.edu/g' README.md
```

Or manually edit:
- `YOUR_USERNAME` → Your GitHub username
- `Your Name` → Your name
- `Your University/Lab` → Your institution
- `your.email@university.edu` → Your email

---

## 🌐 Custom Domain (Optional)

If you have a custom domain:

### 1. Add CNAME file

Create a file named `CNAME` (no extension) with your domain:

```
perovskite.yourdomain.com
```

### 2. Configure DNS

Add a CNAME record in your domain's DNS settings:

| Type | Name | Value |
|------|------|-------|
| CNAME | perovskite | YOUR_USERNAME.github.io |

### 3. Enable HTTPS

In GitHub Pages settings:
- Check "Enforce HTTPS"
- Wait for certificate provisioning (can take up to 24 hours)

### 4. Access your site

```
https://perovskite.yourdomain.com
```

---

## 🔧 Troubleshooting

### Site shows 404

**Problem**: Page not found after deployment

**Solutions**:
1. Wait 1-2 minutes for GitHub to build the site
2. Check that `index.html` is in the root directory
3. Verify GitHub Pages is enabled in Settings → Pages
4. Try accessing with `/index.html` at the end

### Styles not loading

**Problem**: Page appears unstyled

**Solutions**:
1. Check browser console for errors (F12)
2. Verify Chart.js CDN is accessible
3. Clear browser cache (Ctrl+Shift+R)
4. Try a different browser

### Charts not displaying

**Problem**: Graphs are missing

**Solutions**:
1. Check browser console for JavaScript errors
2. Verify Chart.js CDN is loading (check Network tab)
3. Ensure JavaScript is enabled in browser
4. Try disabling ad blockers

### Real-time updates not working

**Note**: The standalone version simulates real-time updates using JavaScript timers. This is expected behavior for the demo.

For actual real-time updates, you would need:
- Backend server with WebSocket support
- Database for storing experiment data
- API endpoints for data access

---

## 📊 Adding Real Data

To replace mock data with real experiments:

### 1. Edit the experiments array in `index.html`

Find this section (around line 500):

```javascript
const experiments = [];
for (let i = 0; i < 50; i++) {
    // Mock data generation
}
```

Replace with your actual data:

```javascript
const experiments = [
    {
        id: 'EXP_001',
        quality: 0.856,
        crystallinity: 0.892,
        status: 'completed',
        composition: 'FA:0.85 Cs:0.15',
        annealTemp: 120
    },
    // Add more experiments...
];
```

### 2. Update metrics

Find and update these values:

```javascript
document.getElementById('completed-count').textContent = '147'; // Your count
// Update progress bar width
// Update average quality
```

### 3. Commit and push

```bash
git add index.html
git commit -m "Update with real experiment data"
git push origin main
```

GitHub Pages will automatically rebuild your site.

---

## 🔄 Updating the Site

To update your deployed site:

### Web Interface

1. Go to your repository on GitHub
2. Click on `index.html`
3. Click the pencil icon (Edit)
4. Make your changes
5. Commit changes

### Command Line

```bash
# Make changes to index.html
git add index.html
git commit -m "Update: description of changes"
git push origin main
```

GitHub Pages will automatically redeploy (takes 1-2 minutes).

---

## 📈 Analytics (Optional)

To track visitors, add Google Analytics:

1. Create a Google Analytics account
2. Get your tracking ID (e.g., `G-XXXXXXXXXX`)
3. Add before `</head>` in `index.html`:

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

---

## 🔒 Security

### Public Repository

**Note**: GitHub Pages sites from public repositories are publicly accessible.

**Do NOT include**:
- API keys or secrets
- Private research data
- Sensitive information

### Private Repository

GitHub Pages is available for private repositories with:
- GitHub Pro
- GitHub Team
- GitHub Enterprise

---

## 🌟 Best Practices

### 1. Use Descriptive Commit Messages

```bash
✅ Good: "Add real experiment data for October 2025"
❌ Bad: "update"
```

### 2. Keep README Updated

Update campaign status regularly:
- Total experiments
- Campaign progress percentage
- Last updated date

### 3. Add Screenshots

Create a `docs/` folder and add screenshots:

```
docs/
├── preview.png
├── dashboard.png
├── status.png
└── experiments.png
```

Reference in README:
```markdown
![Dashboard](docs/dashboard.png)
```

### 4. Version Control

Tag important milestones:

```bash
git tag -a v1.0.0 -m "Initial release"
git push origin v1.0.0
```

---

## 🆘 Getting Help

### GitHub Pages Documentation
https://docs.github.com/en/pages

### Common Issues
https://docs.github.com/en/pages/getting-started-with-github-pages/troubleshooting-404-errors-for-github-pages-sites

### Contact
- Open an issue in your repository
- Check GitHub Community: https://github.community/

---

## ✅ Deployment Checklist

Before going live:

- [ ] Replace `YOUR_USERNAME` in README.md
- [ ] Update personal information (name, email, institution)
- [ ] Add LICENSE file
- [ ] Test locally (open index.html in browser)
- [ ] Create GitHub repository
- [ ] Upload files
- [ ] Enable GitHub Pages
- [ ] Verify site is accessible
- [ ] Test all pages and features
- [ ] Check mobile responsiveness
- [ ] Add repository description
- [ ] Add topics/tags to repository
- [ ] Share your site! 🎉

---

**Congratulations! Your PerovskiteLab is now live! 🚀**

Share your site:
- Twitter/X
- LinkedIn
- Research group
- Academic conferences

---

*Need help? Open an issue or contact the maintainers.*
