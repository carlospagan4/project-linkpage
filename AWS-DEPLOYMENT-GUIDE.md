# Complete AWS Deployment Guide (Console Only)
## Route 53 + CloudFront + S3 Setup

No CLI needed - everything through AWS Console.

---

## 📋 Prerequisites

- AWS Account
- Domain purchased at NameSilo
- Your HTML files ready to upload

**Estimated Time:** 30-45 minutes (mostly waiting for SSL cert and CloudFront deployment)

---

## Step 1: Create S3 Bucket

### 1.1 Go to S3
- Log into AWS Console
- Search for "S3" in the top search bar
- Click "Create bucket"

### 1.2 Bucket Configuration
- **Bucket name:** `your-domain.com` (use your actual domain)
  - Example: `mymusic.com`
- **Region:** Choose closest to your audience (e.g., `us-east-1`)
- **Block Public Access:** UNCHECK "Block all public access"
  - Check the acknowledgment box
- Leave everything else as default
- Click "Create bucket"

### 1.3 Upload Your Files
- Click on your new bucket
- Click "Upload"
- Drag and drop your files:
  ```
  index.html (main-landing-page.html renamed)
  /project-one/
    └── index.html
  /project-two/
    └── index.html
  ```
- Click "Upload"

### 1.4 Enable Static Website Hosting
- In your bucket, go to "Properties" tab
- Scroll to "Static website hosting"
- Click "Edit"
- Select "Enable"
- **Index document:** `index.html`
- **Error document:** `index.html` (optional)
- Click "Save changes"
- **Note the endpoint URL** (you won't use it directly, but good to test)

### 1.5 Set Bucket Policy
- Go to "Permissions" tab
- Scroll to "Bucket policy"
- Click "Edit"
- Paste this policy (replace `your-domain.com` with your bucket name):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::your-domain.com/*"
        }
    ]
}
```

- Click "Save changes"

---

## Step 2: Request SSL Certificate (ACM)

**IMPORTANT:** SSL certificates for CloudFront MUST be created in `us-east-1` region, regardless of where your S3 bucket is.

### 2.1 Switch to US East (N. Virginia)
- In the top-right corner, change region to **US East (N. Virginia)** / `us-east-1`

### 2.2 Go to Certificate Manager
- Search for "Certificate Manager" or "ACM"
- Click "Request certificate"

### 2.3 Request Public Certificate
- Select "Request a public certificate"
- Click "Next"

### 2.4 Certificate Settings
- **Fully qualified domain name:** 
  - Add: `your-domain.com`
  - Click "Add another name to this certificate"
  - Add: `www.your-domain.com`
  - Add: `*.your-domain.com` (for subdomains if you want)
- **Validation method:** DNS validation (recommended)
- Click "Request"

### 2.5 Certificate Pending
- You'll see "Pending validation" status
- Click on your certificate ID
- Expand the domain names
- You'll see CNAME records needed for validation
- **Keep this tab open** - you'll need these values in Step 4

---

## Step 3: Create CloudFront Distribution

### 3.1 Go to CloudFront
- Search for "CloudFront"
- Click "Create distribution"

### 3.2 Origin Settings
- **Origin domain:** 
  - Do NOT select from dropdown
  - Manually enter your S3 website endpoint from Step 1.4
  - Format: `your-bucket-name.s3-website-REGION.amazonaws.com`
  - Example: `mymusic.com.s3-website-us-east-1.amazonaws.com`
  - (Find this in S3 → Properties → Static website hosting)
- **Protocol:** HTTP only (S3 website endpoints don't support HTTPS)
- **Name:** Leave auto-filled or give it a name
- Leave other origin settings as default

### 3.3 Default Cache Behavior
- **Viewer protocol policy:** Redirect HTTP to HTTPS
- **Allowed HTTP methods:** GET, HEAD
- **Cache policy:** CachingOptimized (recommended)
- Leave rest as default

### 3.4 Settings

**⚠️ CRITICAL - Don't skip these settings!**

- **Price class:** Use all edge locations (or choose based on audience)

- **Alternate domain name (CNAME):** ⭐ **MUST ADD YOUR DOMAINS**
  - Click "Add item"
  - Add: `your-domain.com` (e.g., `metalmonkmusic.com`)
  - Click "Add item" again
  - Add: `www.your-domain.com` (e.g., `www.metalmonkmusic.com`)
  - ⚠️ Without this, your domain won't work!

- **Custom SSL certificate:** ⭐ **MUST SELECT YOUR CERTIFICATE**
  - Click the dropdown
  - Select the certificate you created in Step 2 for `your-domain.com`
  - If it doesn't appear:
    - Certificate needs DNS validation first (do Step 4, then come back)
    - Certificate must be in **us-east-1** region
  - ⚠️ You can edit this later if needed, but don't forget!

- **Default root object:** `index.html`
- Leave everything else as default

### 3.5 Create Distribution
- Click "Create distribution"
- **Note the Distribution domain name** (e.g., `d111111abcdef8.cloudfront.net`)
- Status will be "Deploying" (takes 10-20 minutes)
- **Keep this domain name** - you'll need it for DNS

---

## Step 4: Setup Route 53

### 4.1 Create Hosted Zone
- Search for "Route 53"
- Click "Hosted zones" in left sidebar
- Click "Create hosted zone"

### 4.2 Hosted Zone Configuration
- **Domain name:** `your-domain.com` (without www)
- **Type:** Public hosted zone
- Click "Create hosted zone"

### 4.3 Note Nameservers
- You'll see 4 NS (nameserver) records
- Example:
  ```
  ns-123.awsdns-12.com
  ns-456.awsdns-45.net
  ns-789.awsdns-78.co.uk
  ns-012.awsdns-01.org
  ```
- **Copy these** - you'll add them to NameSilo in Step 5

### 4.4 Validate SSL Certificate (from Step 2)
- Go back to Certificate Manager
- Click on your certificate
- For each domain, you'll see CNAME name and value
- In Route 53, click "Create record"
- **Record type:** CNAME
- **Record name:** Copy from ACM (without your domain)
- **Value:** Copy from ACM
- Click "Create records"
- Repeat for each domain in your certificate
- **Wait 5-10 minutes** for validation

---

## Step 5: Update NameSilo Nameservers

### 5.1 Log into NameSilo
- Go to namersilo.com
- Log in to your account
- Go to "Domain Manager"

### 5.2 Change Nameservers
- Click on your domain
- Find "NameServers" section
- Change from "NameSilo Default Nameservers" to custom
- Enter the 4 nameservers from Route 53 (Step 4.3):
  ```
  ns-123.awsdns-12.com
  ns-456.awsdns-45.net
  ns-789.awsdns-78.co.uk
  ns-012.awsdns-01.org
  ```
- Click "Submit"

**⏰ DNS propagation takes 1-48 hours (usually 2-6 hours)**

---

## Step 6: Create DNS Records in Route 53

Go back to Route 53 → Your hosted zone

### 6.1 Create Root Domain Record
- Click "Create record"
- **Record name:** Leave blank (for root domain)
- **Record type:** A
- **Alias:** Toggle ON
- **Route traffic to:** 
  - Choose "Alias to CloudFront distribution"
  - Select your CloudFront distribution from dropdown
- **Routing policy:** Simple routing
- Click "Create records"

### 6.2 Create WWW Record
- Click "Create record"
- **Record name:** `www`
- **Record type:** A
- **Alias:** Toggle ON
- **Route traffic to:**
  - Choose "Alias to CloudFront distribution"
  - Select your CloudFront distribution
- Click "Create records"

---

## Step 7: Wait & Verify

### 7.1 CloudFront Deployment
- Check CloudFront status (takes 15-20 minutes)
- Status should change from "Deploying" to "Enabled"

### 7.2 SSL Certificate
- Check ACM - certificate status should be "Issued"
- If still pending, verify DNS records in Route 53

### 7.3 DNS Propagation
- Use tools to check:
  - https://www.whatsmydns.net/
  - Enter your domain and check NS records
  - Should show your Route 53 nameservers globally

### 7.4 Test Your Site
Once everything is deployed (30-60 minutes total):
- Visit `https://your-domain.com`
- Visit `https://www.your-domain.com`
- Both should work with SSL (padlock icon)

---

## 🎉 You're Done!

Your music projects site is now live with:
- ✅ Custom domain
- ✅ SSL/HTTPS
- ✅ Global CDN (fast worldwide)
- ✅ Professional setup

---

## 🔄 Updating Your Site

When you want to update content:

1. **Upload to S3:**
   - Go to S3 bucket
   - Upload new/changed files
   - Overwrite existing files

2. **Clear CloudFront Cache:**
   - Go to CloudFront → Your distribution
   - Click "Invalidations" tab
   - Click "Create invalidation"
   - Add paths:
     - `/*` (for everything)
     - Or specific paths like `/project-one/*`
   - Click "Create invalidation"
   - Wait 1-2 minutes for cache to clear

---

## 💰 Monthly Costs

- **S3:** ~$0.02 for storage + data transfer
- **CloudFront:** First 1TB free, then ~$0.085/GB
- **Route 53:** $0.50 for hosted zone + $0.40 for queries
- **SSL Certificate:** FREE

**Total: ~$1-2/month** for typical traffic

---

## ⚠️ Troubleshooting

### ⭐ MOST COMMON ISSUE: CloudFront Not Configured for Custom Domain

**Symptom:** Domain doesn't work, or you get errors like "The request could not be satisfied" or CloudFront serves but without SSL.

**Solution:** Verify CloudFront Distribution Settings

1. Go to **CloudFront** in AWS Console
2. Click on your distribution (e.g., `d8e0vp8ebi8xb`)
3. Click **General** tab, then **Edit**

**Check these 3 critical settings:**

✅ **A) Alternate domain names (CNAMEs):**
   - Should list: `your-domain.com` and `www.your-domain.com`
   - If empty or missing → Click "Add item" and add both domains
   - Example: `metalmonkmusic.com` and `www.metalmonkmusic.com`

✅ **B) Custom SSL certificate:**
   - Should show your certificate for `your-domain.com`
   - If it says "Default CloudFront Certificate" → Click dropdown and select your custom certificate from ACM
   - Certificate must be in **us-east-1** region to appear

✅ **C) Default root object:**
   - Should be: `index.html`

4. Click **Save changes**
5. Wait 5-10 minutes for CloudFront to update
6. Test your domain again

---

### Site not loading after setup:
- **First, check CloudFront CNAME/SSL settings above** ⬆️
- Wait longer (DNS takes time)
- Check CloudFront status is "Enabled"
- Verify nameservers updated at NameSilo

### SSL Certificate stuck on "Pending":
- Verify CNAME records in Route 53 match ACM exactly
- Wait 10-20 minutes after adding records
- Check that records don't have your domain name duplicated (common mistake)

### "Access Denied" error:
- Check S3 bucket policy is correct
- Verify CloudFront origin is S3 **website endpoint**, not S3 bucket endpoint
- Format should be: `bucket-name.s3-website-region.amazonaws.com`

### Changes not appearing:
- Create CloudFront invalidation
- Hard refresh browser (Ctrl+Shift+R / Cmd+Shift+R)

### SSL works but domain redirects to CloudFront URL:
- Check that both CNAMEs are added in CloudFront settings
- Verify Route 53 A records point to CloudFront (not blank)

---

## 📝 Quick Reference

**S3 Bucket:** `your-domain.com`  
**CloudFront Distribution:** `d111111abcdef8.cloudfront.net`  
**Route 53 Hosted Zone:** `your-domain.com`  
**Nameservers:** (4 NS records from Route 53)

---

## 🚀 Next Steps

1. Upload your customized HTML files
2. Add more music projects by creating new folders
3. Update content anytime
4. Monitor with AWS billing alerts (set to $5-10/month)
