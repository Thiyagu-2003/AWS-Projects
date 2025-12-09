---

# 🌐 **AWS Static Website Deployment Guide**

### 🚀 *S3 + CloudFront + Certificate Manager + Route 53 (Custom Domain)*

<p align="center">
  <img src="https://img.shields.io/badge/AWS-S3-orange?logo=amazonaws" />
  <img src="https://img.shields.io/badge/AWS-CloudFront-purple?logo=amazonaws" />
  <img src="https://img.shields.io/badge/AWS-Certificate_Manager-green?logo=amazonaws" />
  <img src="https://img.shields.io/badge/AWS-Route_53-blue?logo=amazonaws" />
</p>

---

# 🧭 **Overview**

This guide walks you through deploying a **static website** using:

* 🌩️ **Amazon S3** — hosts your static files
* 🛡️ **AWS Certificate Manager (ACM)** — provides HTTPS certificate
* 🌍 **Amazon CloudFront** — CDN + HTTPS enforcement
* 📡 **Amazon Route 53** — domain DNS configuration

Deploying static sites this way is the correct production method (S3 website hosting directly is outdated and not secure).

---

# 🔶 **1. Configure S3 Bucket**

### 🪣 **Create S3 Bucket**

* Name: **your domain name** (example: `thiyagu.cloud`)
* Uncheck “Block all public access”
* Enable **Static Website Hosting**
* Enter:

  * Index document: `index.html`
  * Error document: `index.html` (for SPA routing)

---

### 🔐 **Bucket Policy (Public Read)**

Paste this policy *exactly* after replacing with your bucket name:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::thiyagu.cloud/*"
    }
  ]
}
```

---

### 📤 **Upload Website Files**

* Upload your **build folder contents only**
* Do **NOT** modify the folder structure
* Keep the filenames exactly as produced by React/Vue/Angular/HTML build

---

# 🔷 **2. Configure Route 53**

### 🌐 **Create Hosted Zone**

* Hosted Zone name **must match your domain**
  Example: `thiyagu.cloud`

### 📌 **Update Nameservers**

* Copy the 4 NS records from Route 53
* Paste them into your domain provider (GoDaddy, Hostinger, Namecheap, etc.)
* Add **WITHOUT the trailing dot**

Example:
`ns-111.awsdns-22.com`  ✔️
`ns-111.awsdns-22.com.` ✖️

DNS propagation: **0–30 minutes** typically.

---

# 🟢 **3. Request SSL Certificate (ACM)**

### 🔏 **Request Certificate**

* Region must be **N. Virginia (us-east-1)** (CloudFront requirement)
* Add both:

```
thiyagu.cloud
www.thiyagu.cloud
```

### 📡 **Add Validation Records**

* Select the certificate → Click **Create Records in Route 53**
* DNS automatically validates in a few minutes

Status becomes: **Issued**

---

# 🟣 **4. Configure CloudFront**

### ⚙️ **Create Distribution**

* Comment: any name
* Origin:
  👉 **Use S3 Website Endpoint**, not the bucket ARN
  Example:
  `http://thiyagu.cloud.s3-website.ap-south-1.amazonaws.com`

### 🔧 Important Settings

* Viewer protocol: `Redirect HTTP to HTTPS`
* WAF: optional
* Caching: recommended defaults

### 🌍 **Add Alternate Domain Names**

```
www.thiyagu.cloud
thiyagu.cloud
```

### 🔐 **Attach the ACM Certificate**

Select the certificate you created earlier.

### ✅ **Create Distribution**

Deployment takes 5–10 minutes.

---

# 🔵 **5. Connect CloudFront with Route 53**

Now Route 53 must point to CloudFront.

### 1️⃣ **Record for main domain**

```
www.thiyagu.cloud
```

* Type: **A**
* Alias: **Yes**
* Target: **CloudFront Distribution**

### 2️⃣ **Record for naked/root domain**

```
thiyagu.cloud
```

* Type: **A**
* Alias: **Yes**
* Target: **CloudFront Distribution**

---

# 🎉 **Deployment Completed**

Once DNS propagates:

### Your website loads securely at:

✅ [https://thiyagu.cloud](https://thiyagu.cloud)
✅ [https://www.thiyagu.cloud](https://www.thiyagu.cloud)

Fast. Secure. CDN-accelerated. Proper production deployment.


---


                                     ┌──────────────────────────────┐
                                     │        End Users             │
                                     │  (Browsers / Mobile / Apps)  │
                                     └───────────────┬──────────────┘
                                                     │
                                                     ▼
                         ┌────────────────────────────────────────────────┐
                         │                Amazon Route 53                 │
                         │         DNS Resolution (A/AAAA Alias)          │
                         └───────────────┬────────────────────────────────┘
                                         │
                                         ▼
                 ┌──────────────────────────────────────────────────────────────┐
                 │                     Amazon CloudFront                        │
                 │   ┌──────────────────────────────────────────────────────┐   │
                 │   │   🌐 Global Edge Network                             │   │
                 │   │   🔒 HTTPS using ACM Certificate                     │   │
                 │   │   ⚡ Caching & Compression                           │   │
                 │   └──────────────────────────────────────────────────────┘   │
                 └───────────────┬──────────────────────────────────────────────┘
                                 │   (Origin Fetch when cache miss)
                                 ▼
        ┌──────────────────────────────────────────────────────────────────────────┐
        │                              Amazon S3                                   │
        │                  (Static Website Hosting Enabled)                         │
        │                                                                            │
        │   ┌──────────────────────────────────────────────────────────────────┐     │
        │   │   📦 S3 Bucket: thiyagu.cloud                                     │     │
        │   │   • Stores index.html, JS, CSS, assets                            │     │
        │   │   • Public Read for website assets                                │     │
        │   │   • Used as CloudFront Origin (Website Endpoint)                  │     │
        │   └──────────────────────────────────────────────────────────────────┘     │
        └──────────────────────────────────────────────────────────────────────────┘


                     ┌────────────────────────────────────────────────────┐
                     │          AWS Certificate Manager (ACM)             │
                     │  • SSL Certificate for:                             │
                     │        - thiyagu.cloud                              │
                     │        - www.thiyagu.cloud                          │
                     │  • Auto-renewed & integrated with CloudFront        │
                     └────────────────────────────────────────────────────┘





---
