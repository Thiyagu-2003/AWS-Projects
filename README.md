---

# 🌐 **AWS Static Website Deployment Guide**

### 🚀 *S3 + CloudFront + Certificate Manager + Route 53 (Custom Domain)*

<p align="center">
  <img src="https://img.shields.io/badge/AWS-S3-orange?logo=amazonaws" />
  <img src="https://img.shields.io/badge/AWS-CloudFront-purple?logo=amazonaws" />
  <img src="https://img.shields.io/badge/AWS-Certificate_Manager-green?logo=amazonaws" />
  <img src="https://img.shields.io/badge/AWS-Route_53-blue?logo=amazonaws" />
  <a href="https://github.com/Thiyagu-2003">
    <img src="https://img.shields.io/badge/Made%20By-Thiyagu%20S-green?logo=github" />
  </a>
</p>

---

# 📑 **Table of Contents**

1. [🧭 Overview](#-overview)
2. [🔶 1. Configure S3 Bucket](#-1-configure-s3-bucket)

   * Create Bucket
   * Enable Static Hosting
   * Bucket Policy
   * Upload Files
3. [🔷 2. Configure Route 53](#-2-configure-route-53)
4. [🟢 3. Request SSL Certificate (ACM)](#-3-request-ssl-certificate-acm)
5. [🟣 4. Configure CloudFront](#-4-configure-cloudfront)
6. [🔵 5. Connect CloudFront with Route 53](#-5-connect-cloudfront-with-route-53)
7. [💠 Full AWS Architecture Diagram](#-full-aws-architecture-diagram)
8. [👤 Author](#-author)

---

# 🧭 **Overview**

This guide walks you through deploying a **secure, fast, production-grade website** using:

* 🌩️ **Amazon S3** — static hosting
* 🛡️ **AWS Certificate Manager (ACM)** — HTTPS/SSL
* 🌍 **Amazon CloudFront** — global CDN
* 📡 **Amazon Route 53** — DNS + domain routing

This is the **industry-standard** way to deploy static websites.

---

# 🔶 **1. Configure S3 Bucket**

## 🪣 **Create S3 Bucket**

* Bucket name: **your domain** → `thiyagu.cloud`
* Disable **Block Public Access**
* Enable **Static Website Hosting**

  * Index: `index.html`
  * Error: `index.html` (for SPA routing)

---

## 🔐 **Bucket Policy (Public Read)**

Replace `thiyagu.cloud` with your bucket name:

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

## 📤 **Upload Website Build**

* Upload **only the contents** of the `build` folder
* Do **not** change file names or structure
* React / Vue / Angular builds should be uploaded as-is

---

# 🔷 **2. Configure Route 53**

## 🌐 **Create Hosted Zone**

* Hosted zone must be: **thiyagu.cloud**

## 📌 **Update Nameservers**

Copy the 4 NS records → paste into your domain provider:

* GoDaddy
* Hostinger
* Namecheap
* BigRock

⚠️ **Remove the dot at the end.**

Example:

```
ns-111.awsdns-22.com    ✔️
ns-111.awsdns-22.com.   ✖️
```

DNS propagation takes **5–30 minutes**.

---

# 🟢 **3. Request SSL Certificate (ACM)**

## 🔏 **Request Certificate**

Region must be **us-east-1 (N. Virginia)**.

Add domains:

```
thiyagu.cloud
www.thiyagu.cloud
```

## 📡 **Validate Certificate**

* Click certificate → **Create records in Route 53**
* Validation happens automatically

Status becomes: **Issued**

---

# 🟣 **4. Configure CloudFront**

## ⚙️ **Create Distribution**

### **Origin Settings**

Use the **S3 Website Endpoint**:

```
http://thiyagu.cloud.s3-website.ap-south-1.amazonaws.com
```

### **Viewer Settings**

| Setting         | Value                 |
| --------------- | --------------------- |
| Viewer protocol | Redirect HTTP → HTTPS |
| Cache policy    | Recommended           |
| WAF             | Optional              |
| Price class     | Use default           |

### **Alternate Domain Names (CNAME)**

```
thiyagu.cloud
www.thiyagu.cloud
```

### **SSL Certificate**

Attach previously created ACM certificate.

Click **Create Distribution**.

Takes **5–10 minutes**.

---

# 🔵 **5. Connect CloudFront with Route 53**

## 1️⃣ Record for `www.thiyagu.cloud`

* Type: **A**
* Alias: **Yes**
* Target: **CloudFront Distribution**

## 2️⃣ Record for root domain `thiyagu.cloud`

* Type: **A**
* Alias: **Yes**
* Target: **CloudFront Distribution**

---

# 🎉 **Deployment Completed**

Your site is now:

* 🌍 CDN-accelerated
* 🔒 HTTPS secure
* 🚀 Fast & globally optimized

### Live at:

✅ [https://thiyagu.cloud](https://thiyagu.cloud)
✅ [https://www.thiyagu.cloud](https://www.thiyagu.cloud)

---

# 💠 **Full AWS Architecture Diagram**
    
```
                
                +-------------------+        +---------------------+        +--------------------+
                |     End User      | <----->|    Amazon Route 53  | <----->|  AWS Certificate   |
                | (Web Browser)     |        |   (Custom Domain)   |        | Manager (ACM)      |
                +-------------------+        +---------------------+        | (SSL/TLS Cert)     |
                         |                               ^                  +--------------------+
                         |                               |                             |
                         | (HTTPS Request)               | (DNS Resolution)            | (Certificate Association)
                         v                               |                             v
                +---------------------+        +---------------------+        +--------------------+
                |  Amazon CloudFront  | <----->|   CloudFront Origin | <----->|    Amazon S3       |
                |   (CDN & Cache)     |        |    Access Control   |        | (Static Website)   |
                | (Edge Locations)    |        |        (OAC)        |        | (HTML, CSS, JS)    |
                +---------------------+        +---------------------+        +--------------------+
                
```

---

## 👤 Author

**Name:** Thiyagu S  
**Role:** Cloud & DevOps Learner  
**Country:** India 🇮🇳  
**GitHub:** [Thiyagu-2003](https://github.com/Thiyagu-2003)

---

## ❤️ Footer

<p align="center">
  <strong>Made with ❤️ by <a href="https://github.com/Thiyagu-2003">Thiyagu S</a></strong><br>
  <em>Learning • Building • Improving</em>
</p>

---

