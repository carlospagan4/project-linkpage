# CloudFront Domain Troubleshooting Checklist

If your custom domain isn't working with CloudFront, run through this checklist:

## ✅ CloudFront Configuration (MOST COMMON FIX)

### Step 1: Check Your Distribution Settings

1. Open **AWS Console** → **CloudFront**
2. Click on your **distribution ID** (e.g., `d8e0vp8ebi8xb`)
3. Click **General** tab
4. Click **Edit** button

### Step 2: Verify These 3 Settings

#### ⭐ A) Alternate domain names (CNAMEs)
```
Should show:
✓ your-domain.com
✓ www.your-domain.com

If empty or missing domains:
→ Click "Add item"
→ Enter your domain (e.g., metalmonkmusic.com)
→ Click "Add item" again  
→ Enter www version (e.g., www.metalmonkmusic.com)
```

#### ⭐ B) Custom SSL certificate
```
Should show:
✓ Certificate for your-domain.com (from ACM)

If shows "Default CloudFront Certificate":
→ Click the dropdown
→ Select your custom certificate
→ (If not listed, certificate must be in us-east-1 region)
```

#### ⭐ C) Default root object
```
Should show:
✓ index.html

If empty:
→ Enter: index.html
```

### Step 3: Save and Wait
- Click **Save changes**
- Wait **5-10 minutes** for CloudFront to update
- Try your domain again

---

## ✅ Route 53 DNS Records

### Check A Records Exist
1. **AWS Console** → **Route 53** → **Hosted zones**
2. Click your domain
3. Verify these records exist:

```
Name: (blank) or @ 
Type: A
Alias: Yes
Target: CloudFront distribution

Name: www
Type: A  
Alias: Yes
Target: CloudFront distribution
```

If missing → Create them pointing to your CloudFront distribution

---

## ✅ SSL Certificate Validation

### Check Certificate Status
1. **AWS Console** → **Certificate Manager** (ACM)
2. Switch to **us-east-1** region (top right)
3. Click your certificate
4. Status should be: **Issued** ✓

If **Pending validation**:
- Check Route 53 has the CNAME validation records
- Wait 10-20 minutes
- Refresh the page

---

## ✅ Nameservers at NameSilo

### Verify NS Records
1. Login to **NameSilo**
2. **Domain Manager** → Your domain
3. Check **NameServers** section

Should show **4 AWS nameservers** like:
```
ns-123.awsdns-12.com
ns-456.awsdns-45.net
ns-789.awsdns-78.co.uk
ns-012.awsdns-01.org
```

If shows NameSilo default NS:
→ Copy the 4 NS records from Route 53 hosted zone
→ Update them in NameSilo
→ Wait 2-6 hours for DNS propagation

---

## ✅ S3 Bucket Configuration

### Check Public Access
1. **AWS Console** → **S3**
2. Click your bucket
3. **Permissions** tab → **Bucket policy**

Should have public read policy:
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

### Check Static Website Hosting
1. **Properties** tab → **Static website hosting**
2. Should be **Enabled** ✓
3. Index document: `index.html`

---

## ✅ CloudFront Origin

### Verify S3 Origin is Website Endpoint

1. **CloudFront** → Your distribution
2. **Origins** tab
3. Click your origin → Edit

**Origin domain** should be:
```
✓ CORRECT: bucket-name.s3-website-us-east-1.amazonaws.com
✗ WRONG:   bucket-name.s3.amazonaws.com
```

If wrong format:
→ Edit origin
→ Manually type the S3 website endpoint
→ Find it in: S3 → Properties → Static website hosting

---

## 🔍 Testing Tools

### Check DNS Propagation
- https://www.whatsmydns.net/
- Enter your domain
- Check if NS records show AWS nameservers globally

### Check SSL Certificate
- Visit: `https://your-domain.com`
- Click padlock icon in browser
- Should show valid SSL for your domain

### Check CloudFront Response
- Open browser dev tools (F12)
- Visit your domain
- Check **Network** tab
- Look for `x-cache` header (should be `Hit from cloudfront` or `Miss from cloudfront`)

---

## ⏰ Common Wait Times

- **CloudFront deploy:** 15-20 minutes
- **SSL validation:** 5-20 minutes
- **DNS propagation:** 2-6 hours (can be up to 48 hours)
- **CloudFront config changes:** 5-10 minutes
- **CloudFront cache invalidation:** 1-2 minutes

---

## 🆘 Still Not Working?

### Clear Everything and Try Again
1. **Clear browser cache** (Ctrl+Shift+Delete)
2. **Clear CloudFront cache** (Create invalidation for `/*`)
3. **Try incognito/private window**
4. **Try different browser**
5. **Try from mobile data** (different network)

### Check for Typos
- Domain spelling in CloudFront CNAMEs
- Domain spelling in Route 53 records
- Certificate domain matches your actual domain

### Error Messages
- **"The request could not be satisfied"** → CloudFront CNAMEs not configured
- **"Access Denied"** → S3 bucket policy or wrong origin endpoint
- **SSL error** → Custom SSL not selected in CloudFront
- **404 on subpages** → CloudFront origin not using S3 website endpoint

---

## 📝 Quick Checklist

Run through this fast:
- [ ] CloudFront has both domain CNAMEs
- [ ] CloudFront has custom SSL certificate selected
- [ ] CloudFront default root object is `index.html`
- [ ] CloudFront status is "Enabled"
- [ ] Route 53 has A records for root and www
- [ ] NameSilo uses Route 53 nameservers
- [ ] SSL certificate status is "Issued"
- [ ] S3 bucket is publicly readable
- [ ] S3 static website hosting is enabled
- [ ] CloudFront origin is S3 website endpoint (not bucket endpoint)
- [ ] Waited at least 30-60 minutes after setup

If all checked ✓ and still not working → Wait longer for DNS propagation!
