# Seamless React Deployment: AWS S3 + CodePipeline + Cloudflare (Step-by-Step Guide)

In modern web development, managing servers is becoming a relic of the past. If you're building a React or Vite project, traditional VPS hosting is often overkill. Enter **Serverless Static Hosting** — a faster, cheaper, and more secure way to deploy your applications.

In this guide, I will walk you through the exact process we use at **Dijital Mecra** to deploy high-performance landing pages using **AWS S3**, **AWS CodePipeline**, and **Cloudflare**.

---

## 💡 Why Choose AWS S3 & Serverless Hosting?
Before we dive into the "How," let's talk about the "Why":
- **Zero Server Management**: No OS updates, no security patches. AWS handles the infrastructure.
- **Cost Efficiency**: You only pay for what you use. Virtually free for small sites.
- **Global Scalability**: S3 integrates with CDNs like Cloudflare for millisecond loading.
- **Top-Tier Security**: No server access means a drastically reduced attack surface.

---

## 🏗️ The Architecture: How It Works
The following vertical diagram illustrates the journey from your keyboard to the user's browser:

```mermaid
graph TD
    subgraph GELISTIRME_FAZI ["1. Development & Source Control"]
        A["💻 Local Code (VS Code)"] -->|Git Push| B["📦 GitHub Repository (Main)"]
    end
    subgraph CI_CD_FAZI ["2. Cloud Automation (AWS)"]
        B -->|Webhook| C["🔄 AWS CodePipeline"]
        C --> D["🏗️ AWS CodeBuild (Build)"]
        D -->|Artifact| E["☁️ AWS S3 Bucket (Hosting)"]
    end
    subgraph DAGITIM_FAZI ["3. Global Access & CDN"]
        E -->|Origin| F["🛡️ Cloudflare DNS/SSL"]
        F --> G["🌍 Global Users"]
    end
    style GELISTIRME_FAZI fill:#f9f9f9,stroke:#333
    style CI_CD_FAZI fill:#e1f5fe,stroke:#01579b
    style DAGITIM_FAZI fill:#fff3e0,stroke:#e65100
```

1. **Source**: Code pushed to GitHub triggers the pipeline.
2. **Build**: CodeBuild runs `npm run build` to generate the `dist` folder.
3. **Deploy**: Files are automatically synced to S3.
4. **CDN**: Cloudflare caches S3 content globally and provide SSL support.

---

## 🛠️ Step-by-Step Setup (12 Steps)

**Step 1: Create Your S3 Bucket**
Head to the S3 console, create a bucket (e.g., `s3-digital-mecra`). Region: `us-east-1` (Vegas).
![S3 Bucket](./public/images/pdf-extracted/page_1.png)
**Step 2: Initialize CodePipeline**
Create a new pipeline. Name: `digital-mecra`. Mode: `Queued`. Allow a new service role.
![Pipeline Settings](./public/images/pdf-extracted/page_2.png)
**Step 3: Connect to GitHub**
Select `GitHub (via OAuth app)` as source. Authorize to allow listening for commits.
![Source Provider](./public/images/pdf-extracted/page_3.png)
**Step 4: Repository and Branch Selection**
Select your repo (`hakanbayraktar/s3-landing-page`) and the `main` branch.
![Repo Selection](./public/images/pdf-extracted/page_4.png)
**Step 5: Configure CodeBuild Environment**
OS: `Amazon Linux 2`. Image: `aws/codebuild/amazonlinux2-x86_64-standard:5.0`.
![Build Environment](./public/images/pdf-extracted/page_5.png)
**Step 6: Buildspec and Logging**
Use the `buildspec.yml` in your root. Enable `CloudWatch logs` for build debugging.
![Buildspec Settings](./public/images/pdf-extracted/page_6.png)
**Step 7: Finalize Build Stage**
Confirm your CodeBuild project and proceed to deployment.
![Build Review](./public/images/pdf-extracted/page_7.png)
**Step 8: Deploy to S3 (Crucial Step)**
**IMPORTANT**: Check the **"Extract file before deploy"** box to serve individual files.
![Deploy Settings](./public/images/pdf-extracted/page_8.png)
**Step 9: Monitor Your First Deployment**
Wait for all three stages (Source, Build, Deploy) to show the green **"Succeeded"** status.
![Successful Pipeline](./public/images/pdf-extracted/page_9.png)
**Step 10: Enable Static Website Hosting**
In S3 Properties, enable **Static website hosting** and specify `index.html` as the index.
![Static Hosting](./public/images/pdf-extracted/page_10.png)
**Step 11: Permissions and Public Access**
Disable "Block all public access" and apply a **Bucket Policy** JSON for global read access.
![Bucket Policy](./public/images/pdf-extracted/page_11.png)
**Step 12: DNS Configuration with Cloudflare**
Add a `CNAME` in Cloudflare pointing to the S3 Website Endpoint. Set to **Proxied**.
![Cloudflare DNS](./public/images/pdf-extracted/page_12.png)

---

**Dijital Mecra** - Modern Web & DevOps Solutions 🌍
