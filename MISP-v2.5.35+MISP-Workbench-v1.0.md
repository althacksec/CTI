# MISP Workbench v1.0 (v2.5.35)

## 1. Genel Bakış 
* **CTI Konusu / Varlık Adı:** MISP Workbench v1.0 (MISP v2.5.35 tabanlı)
* **Kategori:** Security Infrastructure / Threat Intelligence Platform (TIP)
* **Durum:** Active (Deployment/Lab Environment)
* **Kritiklik Seviyesi:** Yüksek (Veri sızıntısı ve pivot noktası riski nedeniyle)

## 2. Teknik Özet
Bu rapor, MISP platformunun v2.5.35 sürümü üzerine inşa edilmiş "Workbench" adlı özel bir dağıtım paketinin güvenlik duruşunu incelemektedir. Bu yapı, genellikle tehdit avcıları (threat hunters) tarafından malware analizi ve IoC korelasyonu için kullanılır. Temel risk, bu tür "Workbench" ortamlarının yanlış yapılandırılması (misconfiguration) durumunda, içerdiği hassas tehdit istihbaratının (IoC, TTP, APT verileri) sızdırılması veya platformun bir "pivot noktası" olarak kullanılarak iç ağa sızılmasıdır.

##ca 3. Tehdit Tanımı ve Problem
**Problem:** MISP Workbench gibi ortamlar, doğası gereği "yüksek güvenilirlik" gerektirir. Bu spesifik versiyon ve yapılandırmanın (v1.0) karşı karşıya olduğu temel riskler:
* **Veri Sızıntısı:** Paylaşılan IoC'lerin ve analiz raporlarının yetkisiz kişilerce ele geçirilmesi.
* **Platform Suistimali:** Saldırganların, platformdaki zafiyetleri kullanarak (örneğin; güvensiz API anahtarları veya zayıf kimlik doğrulama) platformu kendi saldırılarını gizlemek veya ağ içinde hareket etmek (lateral movement) için kullanması.
* **Zehirleme (Poisoning):** Yanlış veya manipüle edilmiş IoC girişleri ile istihbaratın güvenilirliğinin bozulması.

**Hedeflenen Varlıklar:**
* Tehdit İstihbarat Veritabanları (MISP Database)
* Analiz edilen malware örnekleri (Sandbox env.)
* Bağlı olan ağlar ve API entegrasyonları

## 4. Etkilenen Sistemler ve Cihazlar
* **İşletim Sistemleri:** Linux tabanlı dağıtımlar (Genellikle Ubuntu/Debian üzerinde Docker container yapısı).
* **Bulut/On-Prem:** Dockerized yapılar, Cloud (AWS/Azure/GCP) üzerinde çalışan analiz laboratuvarları.
* **Bağımlılıklar:** Redis (Cache), MariaDB/MySQL (Database), Apache/Nginx (Web Server).

## 5. Teknik Detaylar
MISP Workbench v1.0, MISP v2.5.35'in üzerine eklenmiş otomatize edilmiş bir katmandır.

| Bileşen | Teknik Rol | Güvenlik Riski |
| :--- | :--- | :---|
| **MISP Core (v2.5.35)** | Veri yönetimi ve korelasyon | Unauthenticated API access, Insecure plugin usage |
| **Workbench Layer (v1.0)** | Otomasyon ve Sandbox yönetimi | Unhardened container escape, insecure web UI |
| **Python/PyMISP** | Otomasyon scriptleri | Hardcoded credentials, insecure script execution |
| **Redis/MariaDB** | Veri depolama | Unencrypted data at rest, weak access control |

**MITRE ATT&CK Eşleşmesi (Potansiyel Riskler):**
* **T1589 (Gather Victim Identity Information):** Platformdaki verilerin çalınması.
* **T1566 (Phishing):** Platform üzerinden gönderilen sahte uyarılar.
* **T1071 (Application Layer Protocol):** API üzerinden veri sızdırma.

## 6. Saldırı Zinciri 
Saldırganın bu platformu hedef alması durumunda izleyebileceği yol:

1.  **Initial Access:** Zayıf yapılandırılmış web arayüzü (v1.0 Workbench UI) üzerinden brute-force veya zafiyetli bir plugin kullanımı.
2.  **Execution:** Python tabanlı otomasyon scriptleri veya Docker container üzerinde komut çalıştırma.
3.  **Persistence:** Misconfigured API anahtarlarıyla kalıcı bir bağlantı (Websocket/API) kurma.
4.  **Defense Evasion:** Logları temizleme veya sahte IoC'ler ekleyerek tespit mekanizmalarını yanıltma.
5.  **Exfiltration:** Veritabanındaki kritik saldırı raporlarını ve IoC setlerini dışarı sızdırma.

## 7. Tehdit Aktörü Profili
Bu tür bir platformu hedef alan aktörler genellikle:
* **Advanced Persistent Threat (APT) Grupları:** Karşı tarafın savunma kapasitesini (Blue Team) ölçmek ve savunma yöntemlerini öğrenmek için.
* **Cyber Espionage (Siber Casusluk):** Kurumsal zafiyet bilgilerini toplamak için.
**
## 8. Tespit ve Önleme Stratejileri

### **Tespit**
* **Log Analizi:** MISPI veya web sunucu loglarında olağandışı API çağrıları ve başarısız giriş denemelerinin takibi.
* **Anomali Tespiti:** Veri hacmindeki ani artışlar (Egress trafiği analizi).
* **Integrity Monitoring:** Platform dosyalarının (config, python scripts) değiştirilip değiştirilmediğinin izlenmesi.

### **Önleme**
* **Network Segmentation:** MISPI/MISP instance'ının internete doğrudan açık olmaması, sadece VPN/ZTNA üzerinden erişilmesi.
* **Least Privilege:** API anahtarlarının ve kullanıcı yetkilerinin "en az yetki" prensibiyle yapılandırılması.
* **Hardening:** Docker container'ların ve alt servislerin (MariaDB, Redis) güvenlik sıkılaştırmasının yapılması.
* **Input Validation:** Tüm API girişlerinin ve script parametrelerinin sıkı bir şekilde valide edilmesi.

---

*© 2025 AltHack Security — Gerçek Dünya Siber Güvenlik İstihbaratı*
