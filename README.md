# Music Projects Link Tree System

A self-hosted alternative to paid link tree services, designed for hosting multiple music projects on S3.

## 📁 File Structure

```
your-bucket/
├── index.html (main-landing-page.html)
└── project-name/
    └── index.html (project-linkpage-template.html)
```

## 🚀 Setup Instructions

### 1. Main Landing Page
- Use `main-landing-page.html` as your root `index.html`
- Customize the hero section with your name and bio
- Duplicate the project card blocks for each music project
- Update project links to match your folder structure (e.g., `/project-name/index.html`)

### 2. Individual Project Pages
- For each project, create a folder (e.g., `/ambient-project/`)
- Copy `project-linkpage-template.html` as `index.html` in that folder
- Customize each project page:
  - Project name and bio
  - Avatar/cover image
  - Links to streaming services
  - Merch store links
  - Social media links
  - Any other custom links

### 3. Customization Tips

**Colors** - Edit the CSS variables at the top of each file:
```css
--bg-color: #0a0a0a;
--card-bg: #1a1a1a;
--accent-color: #00ff88;
```

**Images** - Replace placeholder images with your own:
- Project page avatars: 120x120px recommended
- Landing page project cards: 400x250px recommended

**Links** - Update all `href` attributes with your actual URLs

## ☁️ S3 Deployment

### Create S3 Bucket
```bash
aws s3 mb s3://your-music-projects
```

### Upload Files
```bash
aws s3 sync . s3://your-music-projects --acl public-read
```

### Enable Static Website Hosting
1. Go to S3 bucket → Properties → Static website hosting
2. Enable it and set index document to `index.html`
3. Note the endpoint URL

### Optional: CloudFront CDN
For better performance and custom domain:
1. Create CloudFront distribution pointing to S3 bucket
2. Set up custom domain with Route53 or your DNS provider
3. Add SSL certificate via ACM

## 💰 Cost Estimate

- S3 Storage: ~$0.023/GB per month
- Data Transfer: First 100GB free, then $0.09/GB
- CloudFront (optional): First 1TB free per month

**Typical cost for 5-10 projects: < $1/month**

## 🎨 Design Features

- Mobile-first responsive design
- Single-file HTML (no external dependencies)
- Fast loading times
- Smooth animations
- Dark mode aesthetic (easily customizable)
- Professional appearance

## 📝 Adding New Projects

1. Create new folder: `/new-project/`
2. Copy `project-linkpage-template.html` as `index.html`
3. Customize the content
4. Add project card to main landing page
5. Upload to S3: `aws s3 sync . s3://your-music-projects`

## 🔧 Maintenance

To update any page:
1. Edit the HTML file locally
2. Upload to S3: `aws s3 cp file.html s3://your-bucket/path/`
3. (Optional) Invalidate CloudFront cache if using CDN

## 🎵 Perfect For

- Multiple music projects/aliases
- Bandcamp, Spotify, Apple Music links
- Merch stores
- Email lists
- Social media profiles
- Direct download links
- Concert/tour info

---

**No monthly fees. Full control. Deploy in minutes.**
