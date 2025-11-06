# My Static Website

A simple **static website** built with HTML and CSS, deployed to **Amazon S3** and served via **Amazon CloudFront** (CDN).

---

## Table of contents

- Overview
- What’s included
- Two deployment options (quick & secure)
  - Option A — Quick: public S3 website (easy, not recommended for production)
  - Option B — Recommended: private S3 + CloudFront (secure, production-ready)
---

## Overview

This repository contains a very small static site:

static-website/
├── index.html
├── style.css
└── README.md


This README explains how to host it on S3 and distribute it reliably and securely with CloudFront.

---

## Why use CloudFront with S3?

- S3 stores the static files reliably and cheaply.  
- CloudFront caches content at edge locations worldwide to reduce latency and provide HTTPS.  
- For production you should avoid making the S3 bucket publicly readable and instead restrict direct S3 access so only CloudFront can fetch objects. This is supported by CloudFront Origin Access Control (OAC). :contentReference[oaicite:0]{index=0}

---

## Option A — Quick (public S3 website)

**When to use:** demos, quick tests, prototypes.

**High-level steps (console):**
1. Create an S3 bucket (name = `your-bucket-name`).
2. Upload `index.html` and `style.css`.
3. In **Properties → Static website hosting**, enable, set `index.html` as Index document.
4. In **Permissions**, disable Block Public Access for the bucket and add a bucket policy that allows public `s3:GetObject`.
5. Use the S3 website endpoint URL to view the site.

**Quick AWS CLI commands (example):**
```bash
# (1) create bucket (choose a region)
aws s3 mb s3://your-bucket-name --region eu-west-1

# (2) upload files
aws s3 sync . s3://your-bucket-name --acl public-read

# (3) enable website hosting (console recommended, but you can use AWS SDKs)
Note: the public-read approach means objects are publicly accessible directly from S3. For production, prefer Option B.
Option B — Recommended (private S3 + CloudFront OAC)

When to use: production, secure hosting, custom domain and HTTPS.

Why: You keep the S3 bucket private and allow CloudFront to access it via an Origin Access Control (OAC). OAC is the newer AWS-recommended method (replaces legacy OAI) and supports modern features like SSE-KMS. 
AWS Documentation

High-level flow

Create a private S3 bucket (block public access ON).

Upload files to the bucket (no public ACL).

Create a CloudFront distribution:

Set S3 bucket as the origin.

Create/enable an Origin Access Control (OAC) for the distribution.

Configure default root object as index.html.

Add an S3 bucket policy that allows the CloudFront OAC (or OAI) to s3:GetObject (example policy below).

Use the CloudFront domain name to access the site (or configure a custom domain + HTTPS).

Step-by-step using Console (recommended for beginners)

Create S3 bucket

Name: your-bucket-name

Region: choose your nearest one

Block Public Access: keep all block public access settings ON (this keeps bucket private).

Upload site files

Upload index.html and style.css to the bucket (no public ACLs).

Create CloudFront distribution

Origin domain: choose your S3 bucket (select the “bucket” origin — not the website endpoint).

Under Origin settings, set Restrict bucket access / choose to create Origin Access Control (OAC) (console will guide you).

Set Default root object to index.html.

Leave default caching behaviour for now; enable HTTPS (CloudFront manages cert).

Create distribution; it typically takes a few minutes to deploy.

Add bucket policy (see example below)

Edit the S3 bucket policy to permit CloudFront's OAC principal s3:GetObject on your bucket objects only from that CloudFront distribution principal (see example JSON).

Test

Use the CloudFront distribution domain (like dxxxxxxxxxxxxx.cloudfront.net) to access the site.
