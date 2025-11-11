# 🌐 ehmi.se - Unified Site

This repository orchestrates the deployment of **ehmi.se** by combining three separate codebases into one unified site.

## 📁 Repository Structure

```
ehmi-se-unified/
├── portfolio/          # Submodule: mrxehmad/portfolio-site (main branch, Next.js)
├── tools/              # Submodule: mrxehmad/online-toolkit (main branch, React)
├── blog/               # Submodule: mrxehmad/blog (blog branch, Publii CMS)
├── .github/workflows/  # GitHub Actions automation
│   └── build-deploy.yml
└── README.md
```

**Note**: The blog submodule tracks the `blog` branch instead of `main`.

## 🎯 Site Structure

The final site combines all three projects:

```
ehmi.se/                    → Portfolio homepage
ehmi.se/about               → Portfolio pages
ehmi.se/contact             → Portfolio pages
ehmi.se/projects            → Portfolio pages

ehmi.se/tools/              → Tools homepage (React SPA)
ehmi.se/tools/*             → 100+ individual tools

ehmi.se/blog/               → Blog homepage (Publii)
ehmi.se/article-name/       → Individual blog posts (at root)
```

## 🚀 How It Works

### Automated Build Process

1. **Triggers**: Workflow runs when:
   - You push to `main` branch
   - Manual trigger via GitHub Actions UI
   - API trigger from submodule repos

2. **Build Steps**:
   - Builds Portfolio (Next.js → static export)
   - Builds Tools (React → `PUBLIC_URL=/tools`)
   - Copies pre-built Blog (Publii output)

3. **Merge Strategy**:
   - Portfolio → base layer at root
   - Tools → `/tools/` directory
   - Blog → `/blog/` + articles at root (no conflicts)

4. **Deploy**: Pushes to `site-prod` branch

### Branch Structure

- `main` - Source code + submodules (workflow lives here)
- `site-prod` - Built site (deploy this branch)

## 🔄 Updating Content

### Update Portfolio
```bash
cd portfolio
git pull origin main
cd ..
git add portfolio
git commit -m "Update portfolio"
git push
```

### Update Tools
```bash
cd tools
git pull origin main
cd ..
git add tools
git commit -m "Update tools"
git push
```

### Update Blog
```bash
cd blog
git pull origin main
cd ..
git add blog
git commit -m "Update blog"
git push
```

### Update All at Once
```bash
git submodule update --remote --merge
git add .
git commit -m "Update all submodules"
git push
```

## ⚙️ Hosting Setup

### Cloudflare Pages (Recommended)

1. Connect this repository to Cloudflare Pages
2. **Build settings**:
   - Branch: `site-prod`
   - Build command: (leave empty - already built by workflow)
   - Build output: `/` (root)
3. Set custom domain: `ehmi.se`
4. Enable "Automatic branch deployments" for `site-prod`

### Alternative: Netlify

1. Connect repository
2. Deploy settings:
   - Branch: `site-prod`
   - Build command: (none)
   - Publish directory: `/`

### Alternative: GitHub Pages

1. Go to Settings → Pages
2. Source: Deploy from branch
3. Branch: `site-prod` / (root)
4. Custom domain: `ehmi.se`

## 🔧 Configuration

### Tools Base Path

The React tools app uses `basename="/tools"` configured via:
- `PUBLIC_URL=/tools` in build command
- Update `package.json` if needed:
  ```json
  {
    "homepage": "/tools"
  }
  ```

### Redirects

The `_redirects` file handles:
- Old subdomain redirects (`tools.ehmi.se` → `ehmi.se/tools`)
- SPA routing for tools (`/tools/*` → `/tools/index.html`)

## 📊 SEO Configuration

### Sitemaps
- Main: `https://ehmi.se/sitemap.xml` (index)
- Portfolio: `https://ehmi.se/sitemap-portfolio.xml`
- Tools: `https://ehmi.se/tools/sitemap.xml`
- Blog: `https://ehmi.se/blog/sitemap.xml`

### Robots.txt
Unified file at root directs crawlers to all sitemaps.

## 🐛 Troubleshooting

### Build fails on portfolio
```bash
cd portfolio
npm ci
npm run build
# Check for errors
```

### Build fails on tools
```bash
cd tools
npm ci
PUBLIC_URL=/tools npm run build
# Check for errors
```

### Submodule out of sync
```bash
git submodule sync
git submodule update --init --recursive
```

### Force rebuild
1. Go to Actions tab
2. Select "Build and Deploy Unified Site"
3. Click "Run workflow" → "Run workflow"

## 📝 Manual Build (Local Testing)

```bash
# Clone with submodules
git clone --recursive https://github.com/mrxehmad/ehmi-se-unified.git
cd ehmi-se-unified

# Build portfolio
cd portfolio
npm ci && npm run build
cd ..

# Build tools
cd tools
npm ci && PUBLIC_URL=/tools npm run build
cd ..

# Merge (same as workflow)
mkdir -p dist
cp -r portfolio/out/* dist/
mkdir -p dist/tools
cp -r tools/build/* dist/tools/
mkdir -p dist/blog
cp blog/output/index.html dist/blog/
cp -r blog/output/assets dist/blog/
# ... (copy blog articles to root)

# Test locally
cd dist
python -m http.server 8000
# Visit: http://localhost:8000
```

## 🔐 Secrets Required

None! The workflow uses `GITHUB_TOKEN` which is automatically provided.

## 📜 License

Each submodule has its own license. See individual repositories for details.

## 🤝 Contributing

To contribute to specific parts:
- **Portfolio**: [mrxehmad/portfolio-site](https://github.com/mrxehmad/portfolio-site)
- **Tools**: [mrxehmad/online-toolkit](https://github.com/mrxehmad/online-toolkit)
- **Blog**: [mrxehmad/blog](https://github.com/mrxehmad/blog)

## 📞 Support

Issues with specific components should be reported to their respective repositories.

---

**Built with ❤️ by Ahmad**