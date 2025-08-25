---
title: "🚀 Fixing Docker Build Error: "failed to solve: node:alpine failed to resolve source metadata""
seoTitle: "Fix Docker Build Error: Failed to Resolve Source Metadata for Node:alp"
seoDescription: "Learn how to fix the Docker build error "failed to resolve source metadata for node:alpine" by updating DNS settings and using reliable registries."
datePublished: Mon Aug 25 2025 09:42:34 GMT+0000 (Coordinated Universal Time)
cuid: cmeqxi3fi001v02l21qzs1t3i
slug: docker-build-error-node-alpine
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/YmwZrfblRHg/upload/23afab168021856289954af520e0a18e.jpeg
tags: tutorial, docker, nodejs, debugging, devops, errors

---

If you’ve been working with Docker and tried running a simple build command like:

```bash
docker build -t welcome-to-docker .
```

you might have faced an error similar to this:

```bash
ERROR: failed to build: failed to solve: node:22-alpine: failed to resolve source metadata for docker.io/library/node:22-alpine: 
failed to copy: httpReadSeeker: failed open: failed to do request: Get "...": dial tcp: lookup docker-images-prod...: no such host
```

This can be really frustrating, especially when all you want is to pull a simple Node.js Alpine image. Don’t worry — you’re not alone. In this blog, I’ll explain **why this error happens and how to fix it step by step**.

---

## 🔍 Why This Error Happens

The error usually means Docker couldn’t pull the image because of **network or DNS issues**. Common reasons include:

* Your system can’t resolve Docker Hub domains due to DNS configuration.
    
* Slow or unstable internet connection.
    
* A restrictive corporate network or proxy.
    
* Outdated Docker Desktop or misconfigured settings.
    

In short, Docker is unable to **connect to Docker Hub and fetch the metadata** for the base image (in this case, `node:22-alpine`).

---

## ✅ Steps to Fix "failed to resolve source metadata" in Docker

### 1\. **Check Your Internet Connection**

Make sure you have a stable connection. If you’re on Wi-Fi, try switching networks or using a mobile hotspot to confirm if the issue is with your ISP.

---

### 2\. **Update Docker Desktop**

Sometimes older versions of Docker Desktop have networking issues.

* Open Docker Desktop → Settings → Check for Updates.
    
* Install the latest version.
    

---

### 3\. **Clear Docker Build Cache**

Run the following commands to clean up:

```bash
docker system prune -af
docker builder prune
```

This removes old build caches and forces Docker to fetch the image again.

---

### 4\. **Change Wi-Fi DNS Settings** (Most Effective 🔥)

This step usually fixes the issue immediately. Your ISP’s DNS may fail to resolve Docker Hub endpoints, so switching to **Google DNS or Cloudflare DNS** helps.

#### On macOS:

1. Go to **System Settings → Network → Wi-Fi → Details → DNS**.
    
2. Remove existing DNS servers and add these:
    
    * `8.8.8.8`
        
    * `8.8.4.4`  
        (Google DNS)  
        or
        
    * `1.1.1.1`
        
    * `1.0.0.1`  
        (Cloudflare DNS)
        
3. Click **Apply** and restart Wi-Fi.
    

#### On Windows:

1. Go to **Control Panel → Network and Sharing Center → Change Adapter Settings**.
    
2. Right-click Wi-Fi → **Properties**.
    
3. Select **Internet Protocol Version 4 (TCP/IPv4)** → Properties.
    
4. Set DNS to:
    
    * Preferred: `8.8.8.8`
        
    * Alternate: `8.8.4.4`
        
5. Save and reconnect.
    

#### On Linux:

Edit `/etc/resolv.conf` and add:

```nginx
nameserver 8.8.8.8
nameserver 8.8.4.4
```

---

### 5\. **Retry Docker Build**

Now rerun your build:

```bash
docker build -t welcome-to-docker .
```

It should successfully fetch `node:22-alpine` without errors. 🎉

---

## 🛠 Final Thoughts

The **Docker "failed to resolve source metadata" error** is almost always a **network/DNS issue** rather than a problem with Docker itself.

By:

* Checking internet,
    
* Updating Docker Desktop,
    
* Clearing build cache, and
    
* Most importantly, **updating DNS settings** to Google/Cloudflare,
    

you can fix this issue and continue building your containers smoothly.

---

👉 Pro tip: If you face this issue frequently, keep your DNS set to Google or Cloudflare permanently for a faster and more reliable developer experience.