# 🚀 Dijital Mecra | Profesyonel AWS S3 & CodePipeline Dağıtım Rehberi

[![AWS](https://img.shields.io/badge/AWS-S3%20%26%20CodePipeline-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-DNS%20%26%20SSL-blue?style=for-the-badge&logo=cloudflare)](https://www.cloudflare.com/)

Bu rehber, **Dijital Mecra** projenizi AWS bulut altyapısı üzerinde nasıl profesyonelce yayına alacağınızı adım adım göstermektedir.

---

## 💡 Neden AWS S3 ve Serverless Hosting?

Geleneksel sunucu (VPS/Dedicated) yönetimi yerine S3 tabanlı statik hosting tercih etmenin temel avantajları şunlardır:
- **Sıfır Sunucu Yönetimi**: OS güncellemeleri, güvenlik yamaları veya sunucu bakımıyla uğraşmazsınız.
- **Maliyet Verimliliği**: Sadece kullandığınız trafik kadar ödeme yaparsınız, boşta duran bir sunucu için ücret ödemezsiniz.
- **Yüksek Performans ve Ölçeklenebilirlik**: S3, Amazon'un global altyapısı üzerinden çalıştığı için trafik aniden artsa bile saniyeler içinde ölçeklenir.
- **Güvenlik**: Sunucu erişimi (SSH) olmadığı için saldırı yüzeyi minimumdur. Cloudflare ile birleştiğinde DDoS koruması ve SSL standart olarak gelir.

---

## 🏗️ Proje Mimarisi (Architecture)

Aşağıdaki şema, kodun yerel geliştirme ortamınızdan global yayına nasıl ulaştığını göstermektedir:

```mermaid
graph LR
    A[Yerel Geliştirme] -->|Git Push| B[GitHub Repo]
    B -->|Webhook| C[AWS CodePipeline]
    subgraph AWS_Cloud ["AWS Cloud (Serverless)"]
        C --> D[AWS CodeBuild]
        D -->|Artifact| E[AWS S3 Bucket]
    end
    E -->|Origin| F[Cloudflare CDN]
    F -->|SSL/DNS| G((Kullanıcılar))
```

1. **Source**: Kod GitHub deposuna yüklendiğinde CodePipeline tetiklenir.
2. **Build**: AWS CodeBuild, `npm run build` komutunu çalıştırarak statik dosyaları üretir.
3. **Deploy**: Üretilen dosyalar otomatik olarak S3 Bucket'ına aktarılır.
4. **CDN**: Cloudflare, S3 üzerindeki içeriği global sunucularına dağıtır ve SSL sağlar.

---

## 🛠️ Kurulum Adımları (12 Adım)

### Adım 1: S3 Bucket Oluşturma
AWS S3 konsoluna gidin, `s3-digital-mecra` adında bir bucket oluşturun. Bölge: `us-east-1`.
![S3 Bucket](./public/images/pdf-extracted/page_1.png)

### Adım 2: CodePipeline Başlangıç
Pipeline ismi: `digital-mecra`. Mod: `Queued`. Otomatik rol oluşturulmasına izin verin.
![Pipeline Settings](./public/images/pdf-extracted/page_2.png)

### Adım 3: Kaynak (GitHub) Bağlantısı
`GitHub (via OAuth app)` seçin ve hesabınızı yetkilendirerek bağlantıyı kurun.
![Source Provider](./public/images/pdf-extracted/page_3.png)

### Adım 4: Depo Seçimi
Depo: `hakanbayraktar/s3-landing-page`, Dal: `main`.
![Repo Selection](./public/images/pdf-extracted/page_4.png)

### Adım 5: CodeBuild Ortamı
İşletim sistemi: `Amazon Linux 2`. Image: `aws/codebuild/amazonlinux2-x86_64-standard:5.0`.
![Build Environment](./public/images/pdf-extracted/page_5.png)

### Adım 6: Buildspec ve Loglar
Projeye dahil edilen `buildspec.yml` talimatlarını kullanın ve logları aktif edin.
![Buildspec Settings](./public/images/pdf-extracted/page_6.png)

### Adım 7: Build Stage Review
Oluşturduğunuz build projesini seçerek aşamayı onaylayın.
![Build Review](./public/images/pdf-extracted/page_7.png)

### Adım 8: S3 Dağıtım ve Kritik Ayar
**KRİTİK**: **Extract file before deploy** kutucuğunu işaretlemeyi unutmayın!
![Deploy Settings](./public/images/pdf-extracted/page_8.png)

### Adım 9: Pipeline İzleme
Tüm aşamaların yeşil (Succeeded) olduğunu doğrulayın.
![Successful Pipeline](./public/images/pdf-extracted/page_9.png)

### Adım 10: Statik Hosting Aktifleştirme
S3 Properties sekmesinden statik hosting'i açın ve `index.html` dosyasını belirtin.
![Static Hosting](./public/images/pdf-extracted/page_10.png)

### Adım 11: İzinler ve Bucket Politikası
"Block all public access" ayarını kapatın ve genel erişim için Bucket Policy (JSON) ekleyin.
![Bucket Policy](./public/images/pdf-extracted/page_11.png)

### Adım 12: Cloudflare CNAME DNS
Cloudflare üzerinden CNAME kaydı oluşturun ve S3 endpoint'inizi hedef gösterin.
![Cloudflare DNS](./public/images/pdf-extracted/page_12.png)

---

**Dijital Mecra** - Modern Web ve DevOps Çözümleri 🌟
