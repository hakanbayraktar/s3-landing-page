# Dijital Mecra | AWS S3 Hosting & Tam Otomatik CI/CD İş Akışı Rehberi 🚀

![Dijital Mecra Banner](./public/banner.png)

Bu proje, bir React uygulamasının **AWS S3** üzerinde statik olarak barındırılmasını ve **AWS CodePipeline** araçları ile uçtan uca otomatik bir **CI/CD (Sürekli Entegrasyon / Sürekli Dağıtım)** hattının nasıl kurulacağını profesyonel düzeyde anlatmaktadır.

Bu rehber, [julien-muke/aws-codepipeline-react-s3](https://github.com/julien-muke/aws-codepipeline-react-s3) eğitim serisi temel alınarak **Dijital Mecra** altyapısına uygun şekilde hazırlanmıştır.

---

## 🏗️ Proje Mimarisi

Daha önce çizmiş olduğumuz bu mimari şema, kodun GitHub'dan başlayıp global CDN ağına (Cloudflare) ulaşana kadar takip ettiği yolu göstermektedir:

```mermaid
graph TD
    subgraph GitHub ["GitHub Repository"]
        SourceCode[Kod Güncelleme / Git Push]
    end

    subgraph AWS_Cloud ["AWS Bulut Platformu"]
        CP[AWS CodePipeline - Orkestrasyon]
        CB[AWS CodeBuild - Derleme ve Paketleme]
        S3_Host[AWS S3 Bucket - Website Hosting]
        S3_Art[AWS S3 Bucket - Pipeline Artifacts]
        
        SourceCode -->|WebHook Tetikleyici| CP
        CP -->|Adım 1: Build| CB
        CB -->|Adım 2: Artifacts Kaydı| S3_Art
        CP -->|Adım 3: S3'e Dağıtım| S3_Host
    end

    subgraph Erişim_Güvenlik ["Domain & SSL Yönetimi"]
        CF[Cloudflare CDN / DNS]
        S3_Host -.->|Statik Web İçeriği| CF
    end

    User((Kullanıcılar)) -->|HTTPS Sorgusu| CF
    CF -->|Önbellek & Proxy| S3_Host
```

---

## 🛠️ Detaylı AWS Kurulum ve Yapılandırma Adımları

Aşağıdaki adımlar, projenizi AWS bulutunda profesyonelce barındırmak için izlemeniz gereken tam prosedürdür.

### Adım 1: GitHub Deposunun Hazırlanması
1. Projenizi GitHub üzerinde yeni bir depoya (repository) pushlayın.
2. Kök dizinde `buildspec.yml` dosyasının bulunduğundan emin olun.
3. *![GitHub Repo Görseli](./public/images/placeholders/github-setup.jpg)*

### Adım 2: Amazon S3 Bucket Kurulumu (Statik Hosting)
1. **Amazon S3** konsoluna gidin ve **"Create bucket"** butonuna tıklayın.
2. **Bucket Name:** `digitalmecra-s3-bucket`
3. **Permissions:** "Block all public access" seçimini kaldırın.
4. **Properties:** En altta **Static website hosting** -> **Enable**.
   - **Index document:** `index.html`
   - **Error document:** `error.html`
5. *![S3 Yapılandırma Görseli](./public/images/placeholders/s3-setup.jpg)*

### Adım 3: AWS CodeBuild Projesinin Yapılandırılması
1. **CodeBuild** konsolunda yeni proje oluşturun.
2. **Environment:** Ubuntu, Standard Runtime, Node.js 18+.
3. **Buildspec:** "Use a buildspec file" seçin.
4. *![CodeBuild Yapılandırma Görseli](./public/images/placeholders/codebuild-setup.jpg)*

### Adım 4: AWS CodePipeline CI/CD Hattının Oluşturulması
1. **Step 1: Choose pipeline settings**
   - **Pipeline name:** `digitalmecra-pipeline`
   - **Service role:** "New service role" seçeneğini işaretleyin. Bu, AWS'in gerekli izinleri otomatik oluşturmasını sağlar.
   - **Execution mode:** "Queued" veya "Superseded" seçebilirsiniz.
2. **Step 2: Add source stage**
   - Source provider: **GitHub (Version 2)**.
3. **Step 3: Add build stage**
   - Build provider: **AWS CodeBuild**.
4. **Step 4: Add deploy stage**
   - Deploy provider: **Amazon S3**.
   - **Bucket:** `digitalmecra-s3-bucket`.
   - **ÖNEMLİ:** "Extract file before deploy" kutucuğunu işaretlemeyi unutmayın!
5. *![CodePipeline Adımları Görseli](./public/images/placeholders/codepipeline-flow.jpg)*

---

## 🌐 Cloudflare ile Custom Domain & SSL (HTTPS) Ayarları

Sitenizin `digitalmecra.devopsatolyesi.com` üzerinden yayınlanması için:

1. **CNAME Kaydı:** 
   - **Name:** `digitalmecra`
   - **Target:** S3 endpoint (Örn: `digitalmecra-s3-bucket.s3-website-us-east-1.amazonaws.com`)
2. **SSL/TLS:** Ayarı **Full** yapın.
3. *![Cloudflare Görseli](./public/images/placeholders/cloudflare-setup.jpg)*

---

## 🚀 Yerel Geliştirme Notları

```bash
# Bağımlılıkları yükleyin
npm install --legacy-peer-deps

# Geliştirme sunucusunu başlatın
npm run dev
```

**Dijital Mecra** - AWS DevOps Eğitim Serisi