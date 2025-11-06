# My Static Website

A simple **static website** built using **HTML and CSS** and deployed to **Amazon S3**, with global content delivery using **Amazon CloudFront**.

---

## 📌 Table of Contents

1. Overview  
2. Prerequisites  
3. Project Structure  
4. Test Locally  
5. Deployment Options  
   - Option A — Quick (Public S3 Website Hosting)  
   - Option B — Recommended (Private S3 + CloudFront)  
6. Example Bucket Policy (for CloudFront OAC)  
7. CloudFront Cache Invalidation  
8. (Optional) Custom Domain + HTTPS  
9. Troubleshooting  
10. Security Notes  
11. Author  

---

## 🔍 Overview

This project contains a basic static website and step-by-step instructions for deploying it to AWS.  
You will learn:

- How to host static content in **Amazon S3**
- How to deliver it globally via **Amazon CloudFront**
- The difference between a **quick/demo setup** and a **secure production setup**

---

## ✅ Prerequisites

Before starting, ensure you have:

| Requirement | Details |
|------------|---------|
| AWS Account | With access to S3 and CloudFront resources |
| AWS Console Login | (No CLI required for basic steps) |
| Git Installed | To manage your project repository |
| (Optional) AWS CLI | If you prefer command-line uploads |

If using AWS CLI, configure it:
```bash
aws configure
📁 Project Structure
static-website/
├── index.html        # Main HTML page
├── style.css         # Stylesheet
└── README.md         # Documentation (this file)
🖥️ Test Locally (Before Uploading)
Open the index.html file directly in your browser.
OR run a simple local server
# Python 3
python -m http.server 8000
🚀 Deployment Options

You have two ways to host this website on AWS:

| Option                 | Recommended For | Security         | Speed        | HTTPS Support |
| ---------------------- | --------------- | ---------------- | ------------ | ------------- |
| Option A (Quick)       | Demos, Learning | ❌ Public Bucket  | Medium       | ❌ Not Native  |
| Option B (Recommended) | Production      | ✅ Private Bucket | ✅ Global CDN | ✅ Full HTTPS  |

✅ Option A — Quick: Public S3 Website (Simple but not secure)

Use only for learning / demos.

Go to AWS Console → S3 → Create Bucket

Give bucket name (example: my-static-site-demo)

Disable Block Public Access

Upload index.html and style.css

Go to Properties → Static website hosting

Enable hosting

Set Index document: index.html

Add public read bucket policy:
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::your-bucket-name/*"
  }]
}

Access the site using the S3 website endpoint
Example:
http://your-bucket-name.s3-website-us-east-1.amazonaws.com

⭐ Option B — Recommended: Private S3 + CloudFront (Secure + HTTPS + Fast)

This is the production-ready method.

Step 1: Create S3 Bucket (Private)

Create bucket in S3

Keep block public access enabled (default)

Upload index.html & style.css

Step 2: Create CloudFront Distribution

Go to CloudFront → Create Distribution

Select S3 bucket as origin (choose bucket, not website endpoint)

Enable Origin Access Control (OAC) — let CloudFront create it

Set default root object to:

index.html

🔒 Security Notes

Avoid public S3 buckets in production.

Always use CloudFront with HTTPS.

Use OAC instead of legacy OAI (new best practice).
Create distribution (wait ~5 mins to deploy)

Step 3: Apply Bucket Policy (Allow CloudFront Only)

AWS may suggest auto-policy. If not, use this:

{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Service": "cloudfront.amazonaws.com"
    },
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::your-bucket-name/*",
    "Condition": {
      "StringEquals": {
        "AWS:SourceArn": "arn:aws:cloudfront::YOUR_AWS_ACCOUNT_ID:distribution/YOUR_DISTRIBUTION_ID"
      }
    }
  }]
}

Step 4: Access Your Website

Copy your CloudFront domain:

https://dxxxxxxxxxx.cloudfront.net
✍️ Author
Sabari
AWS & DevOps Learner | Building Real Hands-On Projects
