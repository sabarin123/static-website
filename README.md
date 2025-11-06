My Static Website

A simple static website built with HTML and CSS, deployed to Amazon S3 and served via Amazon CloudFront (CDN).

Table of contents

Overview

Prerequisites

Project structure

Test locally

Two deployment options

Option A — Quick: public S3 website (fast, not for production)

Option B — Recommended: private S3 + CloudFront (secure, production-ready)

Example S3 bucket policy (for CloudFront access)

CloudFront cache invalidation

Custom domain & HTTPS (short guide)

Automation (GitHub Actions example)

Troubleshooting & FAQ

Security notes

Author

Overview

This repository contains a small static website (HTML + CSS). This README shows clear, copy-pasteable steps to:

host the site on AWS S3, and

(recommended) serve it securely and fast with CloudFront.

Prerequisites

Before you start:

An AWS account with permissions to create S3 buckets, CloudFront distributions, ACM certificates, and IAM/policies.

(Optional, for CLI steps) AWS CLI installed and configured:

aws configure


Basic Git/GitHub knowledge if you want to connect this to a repo/CI.

Project structure
static-website/
├── index.html        # Main HTML file
├── style.css         # Stylesheet
└── README.md         # Project documentation (this file)

Test locally

Open index.html in your browser to view the site locally. No server required for simple static files.

If you want a local static-server (for testing relative paths), you can use Python:

# Python 3
python -m http.server 8000
# then open http://localhost:8000

Two deployment options
Option A — Quick: public S3 website (fast, for demos)

Use when: quick demo or prototype. Not recommended for production because objects are publicly accessible directly from S3.

Console steps (fast):

Create an S3 bucket (unique global name) in a region.

In Properties → Static website hosting, enable and set index.html as index document.

In Permissions, disable Block Public Access for the bucket (only for demo).

Add a bucket policy that allows s3:GetObject for arn:aws:s3:::your-bucket-name/*.

Upload index.html and style.css.

Use the S3 website endpoint (shown in Properties) to open the site.
Option B — Recommended: private S3 + CloudFront (secure, production)

Use when: production, custom domain, HTTPS, security best-practices.

High-level idea: Keep the S3 bucket private. Create a CloudFront distribution and configure an Origin Access Control (OAC) so only CloudFront may read objects from the bucket. CloudFront serves your content from edge locations with HTTPS and caching.

Console step-by-step (recommended for beginners)

Create a private S3 bucket

Name: your-bucket-name (replace placeholder)

Keep Block all public access ON (default) so the bucket is private.

Upload files

Upload index.html and style.css to the bucket.

Do not set public ACLs.

Create a CloudFront distribution

Console → CloudFront → Create distribution.

Origin domain: pick your S3 bucket (choose the bucket resource, not the website endpoint).

Origin access: choose to create an Origin Access Control (OAC) (console will guide you). OAC is the modern replacement for OAI.

Default root object: index.html.

Viewer protocol policy: Redirect HTTP to HTTPS (recommended).

Cache settings: Default is fine for now.

Create distribution (deploys in a few minutes).

Allow CloudFront to read the S3 bucket

When creating the OAC via console, CloudFront often offers to automatically update the bucket policy for you. If you do it manually, add a bucket policy that allows the CloudFront principal (OAI/OAC) to s3:GetObject on arn:aws:s3:::your-bucket-name/*. Example policy is below.

Test

Use CloudFront domain name (e.g. d1234abcd.cloudfront.net) to open your site.
