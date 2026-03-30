# 🚀 Dijital Mecra | Profesyonel AWS S3 & CodePipeline Dağıtım Rehberi

[![AWS](https://img.shields.io/badge/AWS-S3%20%26%20CodePipeline-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-DNS%20%26%20SSL-blue?style=for-the-badge&logo=cloudflare)](https://www.cloudflare.com/)

Bu rehber, **Dijital Mecra** projenizi AWS bulut altyapısı üzerinde nasıl profesyonelce yayına alacağınızı adım adım göstermektedir.

---

## 💡 Neden AWS S3 ve Serverless Hosting?
Geleneksel sunucu yönetimi yerine S3 tabanlı statik hosting tercih etmenin avantajları:
- **Sıfır Sunucu Yönetimi**: OS güncellemeleri veya bakımla uğraşmazsınız.
- **Maliyet Verimliliği**: Sadece kullandığınız trafik kadar ödeme yaparsınız.
- **Yüksek Performans**: Amazon'un global altyapısı ile anında ölçeklenme.
- **Güvenlik**: Sunucu erişimi (SSH) olmadığı için saldırı yüzeyi minimumdur.

---

## 🏗️ Proje Mimarisi (Architecture)
Aşağıdaki dikey şema, kodun yerel geliştirme ortamınızdan globale ulaşma yolculuğunu göstermektedir:

```mermaid
graph TD
    subgraph GELISTIRME_FAZI ["1. Geliştirme & Kaynak Kontrolü"]
        A["💻 Yerel Kod (VS Code)"] -->|Git Push| B["📦 GitHub Deposu (Main)"]
    end
    subgraph CI_CD_FAZI ["2. Bulut Otomasyonu (AWS)"]
        B -->|Webhook| C["🔄 AWS CodePipeline"]
        C --> D["🏗️ AWS CodeBuild (Build)"]
        D -->|Artifact| E["☁️ AWS S3 Bucket (Hosting)"]
    end
    subgraph DAGITIM_FAZI ["3. Global Erişim & CDN"]
        E -->|Origin| F["🛡️ Cloudflare DNS/SSL"]
        F --> G["🌍 Global Kullanıcılar"]
    end
    style GELISTIRME_FAZI fill:#f9f9f9,stroke:#333
    style CI_CD_FAZI fill:#e1f5fe,stroke:#01579b
    style DAGITIM_FAZI fill:#fff3e0,stroke:#e65100
```
1. **Source**: Kod GitHub'a yüklendiğinde Pipeline tetiklenir.
2. **Build**: CodeBuild `npm run build` ile statik dosyaları üretir.
3. **Deploy**: Dosyalar otomatik olarak S3 Bucket'ına aktarılır.
4. **CDN**: Cloudflare global dağıtım ve SSL sağlar.

---

## 🛠️ Kurulum Adımları (12 Adım)

**Adım 1: S3 Bucket Oluşturma**
AWS S3 konsoluna gidin, `s3-digital-mecra` adında bir bucket oluşturun. Bölge: `us-east-1` (Vegas).
![S3 Bucket](./public/images/pdf-extracted/page_1.png)
**Adım 2: CodePipeline Başlangıç**
Pipeline ismi: `digital-mecra`. Mod: `Queued`. Otomatik rol oluşturulmasına izin verin.
![Pipeline Settings](./public/images/pdf-extracted/page_2.png)
**Adım 3: Kaynak (GitHub) Bağlantısı**
`GitHub (via OAuth app)` seçin ve hesabınızı yetkilendirerek bağlantıyı kurun.
![Source Provider](./public/images/pdf-extracted/page_3.png)
**Adım 4: Depo Seçimi**
Depo: `hakanbayraktar/s3-landing-page`, Dal: `main`.
![Repo Selection](./public/images/pdf-extracted/page_4.png)
**Adım 5: CodeBuild Ortamı**
İşletim sistemi: `Amazon Linux 2`. Image: `aws/codebuild/amazonlinux2-x86_64-standard:5.0`.
![Build Environment](./public/images/pdf-extracted/page_5.png)
**Adım 6: Buildspec ve Loglar**
Projeye dahil edilen `buildspec.yml` talimatlarını kullanın ve logları aktif edin.
![Buildspec Settings](./public/images/pdf-extracted/page_6.png)
**Adım 7: Build Stage Review**
Oluşturduğunuz build projesini seçerek aşamayı onaylayın.
![Build Review](./public/images/pdf-extracted/page_7.png)
**Adım 8: S3 Dağıtım ve Kritik Ayar**
**KRİTİK**: **Extract file before deploy** kutucuğunu işaretlemeyi unutmayın!
![Deploy Settings](./public/images/pdf-extracted/page_8.png)
**Adım 9: Pipeline İzleme**
Tüm aşamaların yeşil (Succeeded) olduğunu doğrulayın.
![Successful Pipeline](./public/images/pdf-extracted/page_9.png)
**Adım 10: Statik Hosting Aktifleştirme**
S3 Properties sekmesinden statik hosting'i açın ve `index.html` dosyasını belirtin.
![Static Hosting](./public/images/pdf-extracted/page_10.png)
**Adım 11: İzinler ve Bucket Politikası**
"Block all public access" ayarını kapatın ve genel erişim için Bucket Policy (JSON) ekleyin.
![Bucket Policy](./public/images/pdf-extracted/page_11.png)
**Adım 12: Cloudflare CNAME DNS**
Cloudflare üzerinden CNAME kaydı oluşturun ve S3 endpoint'inizi hedef gösterin.
![Cloudflare DNS](./public/images/pdf-extracted/page_12.png)

---

**Dijital Mecra** - Modern Web ve DevOps Çözümleri 🌟
