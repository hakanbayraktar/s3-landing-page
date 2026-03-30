# 🚀 Dijital Mecra | AWS S3 & Cloudflare Dağıtım Rehberi

[![AWS](https://img.shields.io/badge/AWS-S3%20%26%20CodePipeline-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-DNS%20%26%20SSL-blue?style=for-the-badge&logo=cloudflare)](https://www.cloudflare.com/)
[![DevOps](https://img.shields.io/badge/DevOps-CI%2FCD-green?style=for-the-badge)](https://en.wikipedia.org/wiki/DevOps)

Modern web uygulamalarınızı **AWS S3** üzerinde barındırıp, **AWS CodePipeline** ve **Cloudflare** ile profesyonel bir yayına alma sürecini bu rehberde bulabilirsiniz.

---

## 🏗️ Mimari Yapı

Aşağıdaki şemada, kodun yerel makinenizden başlayıp global olarak nasıl yayına girdiği gösterilmektedir:

```mermaid
graph TD
    subgraph Yerel_Gelisim ["1. Yerel Geliştirme"]
        Dev[Kod Yazımı & Test] --> Push[Git Push]
    end

    subgraph GitHub ["2. Kaynak Kontrolü"]
        Push --> Repo[hakanbayraktar/s3-landing-page]
    end

    subgraph AWS_Bulut ["3. CI/CD Hattı (AWS)"]
        Repo --> CP[AWS CodePipeline]
        CP --> CB[AWS CodeBuild]
        CB -->|Artifacts| S3_Host[AWS S3 - Hosting Bucket]
    end

    subgraph Erisim ["4. Global Erişim (Cloudflare)"]
        S3_Host --> CF[Cloudflare CDN / DNS]
        CF --> User((Kullanıcılar))
    end
```

---

## 🛠️ Adım 1: S3 Bucket & Statik Hosting

İlk aşamada dosyalarımızın barınacağı ana alanı oluşturuyoruz.

> [!IMPORTANT]
> **Bucket İsmi Hakkında**: Yaygın bir yanlış inanışın aksine, Cloudflare ile CNAME kullanırken S3 bucket ismi mutlaka domain adınızla aynı olmak zorunda değildir. Ancak düzenli olması adına benzer bir isim seçmeniz önerilir.

1.  **Bucket Oluşturma**: `s3-digital-mecra` isminde bir bucket oluşturun.
2.  **Statik Web Sitesi**: "Static website hosting" özelliğini aktif edin.

![S3 Bucket Creation](./public/images/pdf-extracted/page_1.png)

---

## 🔄 Adım 2: AWS CodePipeline CI/CD Kurulumu

Kodunuz her değiştiğinde sitenizin otomatik güncellenmesi için bir boru hattı (pipeline) kuruyoruz.

1.  **Pipeline Ayarları**: Queued (Kuyruğa alınmış) modda ve yeni bir `Service Role` ile başlatın.
2.  **Kaynak**: GitHub (OAuth) kullanarak deponuzu bağlayın.
3.  **Build**: AWS CodeBuild kullanarak `npm run build` komutunu çalıştıracak yapılandırmayı yapın.
4.  **Deploy**: Oluşturulan dosyaları seçtiğiniz S3 bucket'ına dağıtın.

| Pipeline Ayarları | Kaynak Aşaması |
| :--- | :--- |
| ![Pipeline Setup](./public/images/pdf-extracted/page_2.png) | ![Source Setup](./public/images/pdf-extracted/page_3.png) |

---

## 🌐 Adım 3: Cloudflare & Custom Domain Bağlantısı

Sitenizi profesyonel bir domain üzerinden (`digitalmecra.devopsatolyesi.com`) yayına almak için Cloudflare ayarlarını yapıyoruz.

1.  **CNAME Kaydı**: Cloudflare DNS panelinden bir `CNAME` kaydı açın.
2.  **Hedef (Target)**: Hedef olarak S3 **Static Web Site Endpoint** adresini girin (Örn: `s3-digital-mecra.s3-website-us-east-1.amazonaws.com`).
3.  **Proxy Status**: "Proxied" (Turuncu Bulut) durumuna getirin.

![Cloudflare CNAME](./public/images/pdf-extracted/page_12.png)

---

## 🚨 Kritik Yapılandırmalar: CORS ve İzinler

Sitenizin sorunsuz çalışması (özellikle asset'lerin yüklenmesi) için aşağıdaki iki ayar hayati önem taşır.

### 1️⃣ CORS Ayarı (Cross-Origin Resource Sharing)
Farklı kökenlerden gelen isteklerin (Cloudflare -> S3) reddedilmemesi için S3 Bucket -> Permissions -> CORS kısmına şu JSON'u ekleyin:

```json
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "HEAD"],
        "AllowedOrigins": ["*"],
        "ExposedHeaders": []
    }
]
```

### 2️⃣ Bucket Policy (İzinler)
Dosyaların dünya genelinden okunabilmesi (403 Forbidden hatası almamak için):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::s3-digital-mecra/*"
        }
    ]
}
```

---

## 🚀 Yerel Geliştirme

Projeyi yerelde çalıştırmak için:

```bash
# Bağımlılıkları yükleyin
npm install --legacy-peer-deps

# Geliştirme sunucusunu başlatın
npm run dev
```

**Dijital Mecra** - Modern Web ve DevOps Çözümleri 🌟