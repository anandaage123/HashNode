---
title: "How to Deploy Your First Node.js Application on AWS EC2 (Step-by-Step Guide)"
seoTitle: "How to Deploy Your First Node.js Application on AWS EC2 (Step-by-Step "
seoDescription: "Learn how to deploy your first Node.js application on AWS EC2 in a simple step-by-step guide. Covers EC2 setup, SSH, Node.js installation, environment varia"
datePublished: Fri Sep 05 2025 07:25:55 GMT+0000 (Coordinated Universal Time)
cuid: cmf6igq3j000002js3j5q0lzx
slug: how-to-deploy-your-first-nodejs-application-on-aws-ec2-step-by-step-guide
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/FHnnjk1Yj7Y/upload/ee688e0813e984018e6d44699f886694.jpeg
tags: ec2, aws, deployment, nodejs, devops

---

Deploying your first Node.js app on AWS can feel overwhelming. But once you understand the steps, it’s straightforward. In this guide, we’ll go through the **exact process** of launching a Node.js application on AWS EC2, making it publicly accessible, and keeping it running in production.

---

## 1️⃣ Launch an EC2 Instance

1. Log in to your AWS Console.
    
2. Navigate to **EC2 → Launch Instance**.
    
3. Choose **Ubuntu** as your OS.
    
4. Select instance type (e.g., `t2.micro` for free tier).
    
5. Create or use an existing **key pair** (download `.pem` file).
    
6. Configure **Security Group** rules:
    
    * **SSH (22)** → Your IP
        
    * **HTTP (80)** → Anywhere
        
    * **Custom TCP (3000)** → Anywhere (for testing Node.js directly)
        

Click **Launch Instance**.

---

## 2️⃣ Connect to Your Instance

From your local terminal:

```bash
chmod 400 key.pem
ssh -i key.pem ubuntu@<EC2_Public_IP>
```

---

## 3️⃣ Install Node.js and Git

Inside your EC2 instance:

```bash
sudo apt update -y
sudo apt install -y nodejs npm git
```

---

## 4️⃣ Clone Your App and Setup Environment Variables

1. Clone your repository:
    
    ```bash
    git clone <your_repo_url>
    cd <project_folder>
    npm install
    ```
    
2. Create a `.env` file if your app requires API keys (Stripe, DB, etc.):
    
    ```bash
    nano .env
    ```
    
    Example:
    
    ```bash
    PORT=3000
    STRIPE_SECRET_KEY=sk_test_1234567890
    DATABASE_URL=mongodb+srv://user:password@cluster/db
    ```
    
3. Load `.env` variables in your app using [dotenv](https://www.npmjs.com/package/dotenv?utm_source=chatgpt.com):
    
    ```bash
    npm install dotenv
    ```
    
    ```bash
    require('dotenv').config();
    const express = require('express');
    const app = express();
    
    const PORT = process.env.PORT || 3000;
    
    app.listen(PORT, '0.0.0.0', () => {
      console.log(`Server running on port ${PORT}`);
    });
    ```
    
4. Add `.env` to `.gitignore`:
    
    ```bash
    .env
    ```
    

---

## 5️⃣ Run the Application

```bash
npm start
```

Check your app in the browser:

```bash
http://<EC2_Public_IP>:3000
```

---

## 6️⃣ Keep the App Running with PM2

Instead of running `npm start` manually, use PM2 to keep your app alive:

```bash
sudo npm install -g pm2
pm2 start app.js
pm2 startup
```

---

## 7️⃣ (Optional) Use Nginx for Port 80

By default, Node.js apps run on `:3000`. To access via `http://<EC2_IP>` without the port:

1. Install Nginx:
    
    ```bash
    sudo apt install -y nginx
    ```
    
2. Configure reverse proxy:
    
    ```bash
    sudo nano /etc/nginx/sites-available/default
    ```
    
    Inside `server { ... }` add:
    
    ```nginx
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    ```
    
3. Restart Nginx:
    
    ```bash
    sudo systemctl restart nginx
    ```
    

Now your app is live at:

```bash
http://<EC2_Public_IP>
```

---

## 8️⃣ Final Step: Configure Inbound Rules

Go to your **EC2 → Security Groups → Inbound Rules** and make sure you have:

* **SSH (22)** → Your IP
    
* **HTTP (80)** → Anywhere
    
* **Custom TCP (3000)** → Anywhere (optional if using Nginx)
    

✅ This ensures your app is publicly accessible.

---

## 🎉 Conclusion

You’ve just deployed your first Node.js app on AWS EC2! 🚀  
Here’s what you achieved:

* Launched an EC2 instance
    
* Installed Node.js and Git
    
* Set up environment variables securely
    
* Ran and kept your app alive with PM2
    
* Configured Nginx for production-ready access
    
* Opened inbound rules for public access
    

From here, you can:

* Attach a **domain name** via Route 53
    
* Add **SSL certificates (HTTPS)** with Let’s Encrypt
    
* Automate deployments with **CI/CD pipelines**
    

Your first step into cloud deployment is complete. 👏