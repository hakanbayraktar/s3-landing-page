# 🚀 Dijital Mecra | AWS S3 & CodePipeline Dağıtım Rehberi

[![AWS](https://img.shields.io/badge/AWS-S3%20%26%20CodePipeline-orange?style=for-the-badge&logo=amazon-aws)](https://aws.amazon.com/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-DNS%20%26%20SSL-blue?style=for-the-badge&logo=cloudflare)](https://www.cloudflare.com/)

Bu rehber, **Dijital Mecra** projenizi AWS bulut altyapısı üzerinde nasıl profesyonelce yayına alacağınızı adım adım göstermektedir.

---

## 🛠️ Adım 1: S3 Bucket Oluşturma

Sitenizin barınacağı ana alanı oluşturuyoruz.
1. AWS S3 konsoluna gidin ve **Create bucket** butonuna tıklayın.
2. **Bucket name**: `s3-digital-mecra` olarak belirleyin.
3. **Region**: `us-east-1` (N. Virginia) seçeneğini işaretleyin.
4. Diğer ayarları varsayılan bırakıp bucket'ı oluşturun.

![S3 Bucket Creation](./public/images/pdf-extracted/page_1.png)

---

## 🛠️ Adım 2: CodePipeline Başlangıç Ayarları

CI/CD hattınızı (otomatik güncelleme hattı) oluşturun.
1. CodePipeline konsolunda **Create pipeline** butonuna tıklayın.
2. **Pipeline name**: `digital-mecra` yazın.
3. **Execution mode**: `Queued` seçeneğini işaretleyin.
4. **Service role**: "New service role" seçerek sistemin otomatik rol oluşturmasına izin verin.

![Pipeline Settings](./public/images/pdf-extracted/page_2.png)

---

## 🛠️ Adım 3: Kaynak (Source) Bağlantısı

Kodunuzun çekileceği platformu bağlayın.
1. **Source provider**: `GitHub (via OAuth app)` seçin.
2. **Connect to GitHub** butonuna basarak hesabınızı yetkilendirin.

![Source Provider](./public/images/pdf-extracted/page_3.png)

---

## 🛠️ Adım 4: Depo ve Branch Seçimi

Hangi projenin canlıya alınacağını belirleyin.
1. **Repository**: `hakanbayraktar/s3-landing-page` deposunu seçin.
2. **Branch**: `main` dalını seçerek her güncellemede tetiklenmesini sağlayın.

![Repo Selection](./public/images/pdf-extracted/page_4.png)

---

## 🛠️ Adım 5: CodeBuild Ortam Yapılandırması

Kodun nasıl derleneceğini (Build) ayarlayın.
1. **Environment image**: `Managed image` seçin.
2. **Operating system**: `Amazon Linux 2`.
3. **Runtime(s)**: `Standard`.
4. **Image**: `aws/codebuild/amazonlinux2-x86_64-standard:5.0`.
5. **Service Role**: Yeni bir rol oluşturulmasını (`codebuild-digital-mecra-service-role`) sağlayın.

![Build Environment](./public/images/pdf-extracted/page_5.png)

---

## 🛠️ Adım 6: Buildspec ve Log Ayarları

Derleme talimatlarını ve izleme ayarlarını yapın.
1. **Buildspec**: Proje kök dizinindeki `buildspec.yml` dosyasını kullanmak için `Use a buildspec file` seçin.
2. **Logs**: Sorun giderme için `CloudWatch logs` özelliğini aktif edin ve bir grup adı girin.

![Buildspec Settings](./public/images/pdf-extracted/page_6.png)

---

## 🛠️ Adım 7: Build Aşaması Onaylama

Oluşturduğunuz build projesini pipeline'a ekleyin.
1. **Build provider**: `AWS CodeBuild`.
2. **Project name**: Bir önceki adımda oluşturduğunuz `digital-mecra` projesini seçin.

![Build Review](./public/images/pdf-extracted/page_7.png)

---

## 🛠️ Adım 8: S3 Dağıtım (Deploy) Ayarları

Derlenen dosyaların S3'e nasıl kopyalanacağını belirleyin.
1. **Deploy provider**: `Amazon S3`.
2. **Bucket**: `s3-digital-mecra`.
3. **KRİTİK**: **Extract file before deploy** (Dağıtımdan önce dosyayı çıkar) kutucuğunu mutlaka işaretleyin.

![Deploy Settings](./public/images/pdf-extracted/page_8.png)

---

## 🛠️ Adım 9: Pipeline İzleme ve Doğrulama

Hattın ilk çalışmasını kontrol edin.
1. Tüm aşamaların (Source, Build, Deploy) yeşil olduğunu ve **Succeeded** yazdığını doğrulayın.

![Successful Pipeline](./public/images/pdf-extracted/page_9.png)

---

## 🛠️ Adım 10: S3 Statik Hosting Aktifleştirme

Sitenizi bir web adresi olarak açın.
1. S3 bucket -> **Properties** sekmesine gidin.
2. En alttaki **Static website hosting** kısmında **Edit** deyin.
3. **Enable** seçin ve **Index document** kısmına `index.html` yazın.

![Static Hosting](./public/images/pdf-extracted/page_10.png)

---

## 🛠️ Adım 11: İzinler ve Bucket Politikası

Sitenin dünya genelinden erişilebilir olmasını sağlayın.
1. **Permissions** sekmesine gidin.
2. **Block all public access** ayarını **Off** (Kapalı) yapın.
3. **Bucket policy** kısmına aşağıdaki JSON politikayı (kendi bucket isminizle) ekleyin:

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

![Bucket Policy](./public/images/pdf-extracted/page_11.png)

---

## 🛠️ Adım 12: Cloudflare CNAME Kaydı

Özel domain bağlantısını (Custom Domain) tamamlayın.
1. Cloudflare DNS kısmına gidin.
2. Bir `CNAME` kaydı ekleyin.
3. **Name**: `domain-adinizi` girin.
4. **Target**: S3 Static Web Site Endpoint adresinizi yapıştırın.
5. **Proxy status**: Turuncu (Proxied) olduğundan emin olun.

![Cloudflare DNS](./public/images/pdf-extracted/page_12.png)

---

> [!NOTE]
> **Önemli Hatırlatma**: S3 izinleri kısmında ayrıca bir **CORS** (Cross-Origin Resource Sharing) ayarı yapmanız, özellikle font ve icon dosyalarının Cloudflare üzerinden hatasız yüklenmesi için gerekebilir.

**Dijital Mecra** - Modern Web ve DevOps Çözümleri 🌟