# Cloud Security Cheatsheet Örneği (2026 Güncel)

Bu cheatsheet, bulut güvenliği için temel best practice'leri, yaygın hataları ve araçları özetlemekte. AWS, Azure ve GCP gibi popüler sağlayıcılara odaklanarak hazırlandı.
İlham kaynakları: OWASP Secure Cloud Architecture Cheat Sheet, Wiz Advanced Cloud Security Best Practices ve SecPod Cloud Security Cheat Sheet. Kendi repo'na eklemek için Markdown formatında kullanabilirsin – güncelle ve genişlet!

## Giriş
Bulut güvenliği, shared responsibility model'e dayanır: Sağlayıcı altyapıyı korur, biz ise konfigürasyon, erişim ve uygulamaları yönetiriz. Ana odak, risk analizi, tehdit modelleme ve attack surface minimizasyonudur. Yüksek riskli uygulamalarda zero-trust uygulamak en iyisidir.

## Yaygın Misconfigurations ve Düzeltmeler
Aşağıdaki tablo, en sık görülen hataları ve düzeltmeleri listelemekte. (AWS ve Azure örnekleriyle)

| Misconfiguration | Açıklama | Düzeltme (AWS) | Düzeltme (Azure) |
|------------------|----------|----------------|------------------|
| Publicly Accessible Storage | S3 bucket'lar veya Blob'lar herkese açık, veri sızıntısı riski içermekte. | S3 Block Public Access'i etkinleştirerek, bucket policy'lerle IAM'i kısıtlayıp; SSE-KMS ile şifrele. | Blob container'ı "Private" yap; firewall ve VNet ile kısıtla; SSE + Key Vault entegre et. |
| Overly Permissive IAM Policies | Yıldız (*) kullanımıyla elde edilen geniş izinler, privilege escalation riski içeriyor. | IAM Access Analyzer kullan; spesifik action/resource tanımla; SCP'ler uygula. | RBAC ile fine-grained roller; Privileged Identity Management ile audit et; scope sınırlı tut. |
| Hardcoded/Exposed Secrets | Kodda veya repo'da credentials, theft riski içermekte. | Secrets Manager/Parameter Store kullan; otomatik rotate et; repo scan et. | Key Vault'ta sakla; access policy'ler ve logging etkinleştir; scanner'larla tarat. |
| Unrestricted Inbound Access | Açık portlar (SSH/RDP) herkese açık, brute-force atağı tehlikesi içermekte. | Security Groups ile IP/CIDR kısıtla; NACL ve WAF ekle. | NSG'lerle kısıtla; Just-In-Time Access etkinleştir; DDoS Protection kullan. |
| Disabled Logging/Monitoring | Log'lar devre dışı kalıyor, ataklar fark edilememekte. | CloudTrail ve AWS Config etkinleştir; SIEM entegre et. | Activity/Diagnostic Logs; Azure Monitor + Defender for Cloud kullan. |

Bu hatalar attack surface'ı %70-90 artırır. Least privilege ve encryption ile önüne geçmeye çalışırız.

## Best Practices
- **Infrastructure Security**: VM'leri harden et (e.g., Linux komutları: `sudo apt update && sudo apt upgrade`), storage'ı secure et, network'ü segment et (VPC/subnet, NACL/security groups).
- **Application Security**: Secure coding uygula (parametreli SQL queries), third-party library'leri güncelle, log'ları monitor et (e.g., Filebeat config).
- **IAM**: Access key'leri rotate et (AWS CLI: `aws iam create-access-key`), IAM role'ler kullan, unusual access'i tespit et.
- **Incident Response**: Plan hazırla, drill'ler yap, otomatik threat detection (e.g., compromised EC2'yi izole et: `aws ec2 modify-instance-attribute`).
- **Data Protection**: Compliance check'ler yap (AWS Config), threat intelligence entegre et (MISP gibi).
- **Security Tooling**: WAF ile atakları blokla (rate limiting, custom rules), logging için trace ID'ler kullan, DDoS protection etkinleştir.

## Önerilen Araçlar
- **WAF & Firewall**: AWS WAF, Azure Firewall – atakları (XSS, SQLi) blokla.
- **Monitoring**: CloudTrail (AWS), Azure Monitor – anomaly detection için.
- **IAM Tools**: IAM Access Analyzer (AWS), Privileged Identity Management (Azure).
- **Threat Detection**: Microsoft Defender for Cloud, Wiz – AI tabanlı scanning.
- **Secrets Management**: AWS Secrets Manager, Azure Key Vault.

## Basit Architecture Örneği
Basit bir setup: Internet gateway > Public subnet (load balancer) > Private subnet (database/backend). Bu, backend'i korur ve entry point'lere odaklanır.

Bulut güvenliği mimarisi diagramı örnekleri:

<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/6ec17e18-7cac-41aa-baf0-0c1ecfee720a" />
<img width="747" height="430" alt="image" src="https://github.com/user-attachments/assets/da78db91-75f5-4946-9626-8cd6daa4a47a" />
<img width="900" height="500" alt="image" src="https://github.com/user-attachments/assets/bd8b7663-274d-41bc-93df-da52793a7fa0" />
