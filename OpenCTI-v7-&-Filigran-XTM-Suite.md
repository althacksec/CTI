# Siber Tehdit İstihbaratı Altyapı Analiz Raporu: OpenCTI-t7 & Filigran XTM Suite

**Rapor Tarihi:** 24 Mayıs 2024  
**Rapor Sınıflandırması:** Kurumsal / Teknik  
**Analist:** Senior CTI Analyst  
**Konu:** OpenCTI-v7 & Filigran XTM Suite Yetenek ve Mimari Analizi

---

### 1. Genel Bakış (Overview)
* **CTI Konusu:** OpenCTI-v7 & Filigran XTM Suite (Güvenlik Altyapısı/Platformu)
* **Kategori:** Cyber Threat Intelligence Management Platform (CTIP) / Security Orchestration
* **Durum:** Active (Yaygın kullanımda ve sürekli güncellenlenen bir ekosistem)

### 2. Teknik Özet
OpenCTI-v7 ve Filigran XTM Suite, dağınık haldeki siber tehdit verilerini (logs, feeds, reports, malware analysis) tek bir merkezi "Knowledge Graph" (Bilgi Grafiği) üzerinde toplamak, ilişkilendirmek ve anlamlı istihbarata dönüştürmek için tasarlanmış kapsamlı bir platformdur. Bu suite, STIX2 standartlarını temel alarak verilerin sadece depolanmasını değil, aynı zamanda otomatik olarak zenginleştirilmesini (enrichment) ve görselleştirilmesini sağlar.

### 3. Tehdit Tanımı ve Problem (Intelligence Gap)
Bu platformun çözüm sunduğu temel problem, **"Intelligence Fragmentation" (İstihbarat Parçalanması)** durumudur.
* **Problem:** Tehdit verilerinin farklı kaynaklardan (OSINT, ISAC, ISAC, Internal Logs) düzensiz gelmesi, verilerin birbiriyle ilişkilendirilememesi ve analistin manuel veri toplama süreçlerinde kaybettiği zaman.
* **Hedef Alınan Varlıklar:** SOC ekipleri, Threat Hunting birimleri, IR (Incident Response) ekipleri ve kurumsal siber güvenlik yönetim katmanı.

### rol 4. Etkilenen Sistemler ve Cihazlar
Platformun entegre olduğu ve operasyonel olarak yönettiği ekosistem:
* **Güvenlik Araçları:** SIEM (Splunk, Microsoft Sentinel), EDR/XDR (CrowdStrike, SentinelOne), IDS/IPS.
* **Veri Kaynakları:** Threat Intelligence Feeds (AlienVault, MISP), Malware Sandbox (Cuckoo, Any.Run), Cloud Logları (AWS CloudTrail, Azure Monitor).
* **Altyapı:** On-premise sunucular veya Cloud-native (Docker/Kubernetes) ortamlar.

### 5. Teknik Detaylar
Platformun çalışma mantığı, veriyi bir "Graph" yapısı olarak ele almaktır.
* **Mimari Yapı:** Python tabanlı mikroservis mimarisi.
* **Veri Modeli:** STIX2 (Structured Threat Information Expression) standardı.
* **Veri Depolama & İşleme:** 
    * **Elasticsearch:** Arama ve indeksleme.
    * **Redis:** Önbellekleme ve kuyruk yönetimi.
    * **RabbitMQ:** Servisler arası mesajlaşma.
* **Yetenekler:** Otomatik veri zenginleştirme (Enrichment), ilişki kurma (Relationship mapping), ve API tabanlı veri dağıtımı.

### 6. İstihbarat Yaşam Döngüsü Analizi (Intelligence Lifecycle)
*Platformun bir saldırı zinciri değil, bir istihbarat döngüsü yönettiği varsayılarak analiz edilmiştir:*

| Aşama | Platformın Rolü (XTM Suite) | İlgili Teknik / İşlem |
| :---| :--- | :--- |
| **Collection** | Farklı kaynaklardan (RSS, API, File) veri çekme. | Data Ingestion (Connectors) |
| **Processing** | Ham verinin STIX2 formatına dönüştürülmesi. | Parsing & Standardization |
| **Analysis** | Veriler arası korelasyon ve pattern tanıma. | Knowledge Graph Analysis |
| **Enrichment** | IP/Domain bilgilerinin Whois, VirusTotal vb. ile zenginleştirilmesi. | Automated Enrichment |
| **Dissemination** | Hazırlanan istihbaratın SIEM/EDR'a iletilmesi. | API / Export / Integration |

### 7. Tehdit Aktörü Karşılaştırması (Contextual Intelligence)
Platform, tehdit aktörlerini (APT, Cybercrime vb.) şu şekilde modellere ayırır:
* **Profilleme:** Aktörün motivasyonu (Espionage, Financial), hedef sektörleri ve kullandığı TTP'ler.
* **Korelasyon:** Bir IP adresinin hangi APT grubuyla ilişkili olduğunun otomatik olarak tespiti.

### 8. Göstergeler (Indicators of Use/Health)
*Not: Bu bölüm, platformun sağlıklı çalışmasını veya platformun bir hedef olarak izlenmesini sağlayan teknik izler içindir.*
* **API Traffic:** `POST /api/` uç noktalarına gelen yoğun/anormal istekler.
* **Connector Logs:** Veri besleme (ingestion) süreçlerindeki başarısızlıklar (Error 500, Timeout).
* **Data Volume Spikes:** Belirli bir IoC tipinde (örneğin Domain) ani artışlar (Potential Campaign detection).

### 9. Detection (Tespit) Senaryoları
OpenCTI kullanımıyla oluşturulabilecek SIEM/EDR use-case'leri:
* **IoC Matching:** OpenCTI'dan gelen güncel "Malicious IP" listesinin SIEM üzerinden Firewall logları ile eşleştirilmesi.
* **Pattern Detection:** Belirli bir saldırı grubunun (örn: APT28) kullandığı tekniklerin (MITRE ATT&CK) ağ trafiğinde görülmesi.
* **Anomalous Connection:** Platformda "High Confidence" olarak işaretlenen bir domain ile gerçekleşen outbound bağlantıların alarm üretmesi.

### 10. Mitigation & Defense (Platform Güvenliği ve Strateji)
* **Kısa Vadeli:** API anahtarlarının (API Keys) sıkı yönetimi ve RBAC (Role-Based Access Control) uygulanması.
* **Uzun Vadeli:** Platformun dış dünyaya kapalı, sadece güvenli bir yönetim ağında (Management Network) tutulması.
* **Hardening:** Docker container imajlarının düzenli taranması ve zayıf şifreleme algoritmalarının devre dışı bırakılması.

### 11. Incident Response (Olay Müdahale)
Platformun IR sürecine katkısı:
* **Containment:** Tespit edilen zararlı IoC'lerin anında güvenlik duvarlarına (Firewall) "Block" listesi olarak iletilmesi.
* **Eradication:** Zararlı yazılımın kullandığı persistence (kalıcılık) mekanizmalarının (Registry, Service) OpenCTI üzerinden tespit edilerek temizlenmesi.
* **Recovery:** Olay sonrası çıkarılan bulguların (Lessons Learned) platforma kaydedilerek gelecekteki saldırılar için eğitici veri (Intelligence) haline getirilmesi.

### 12. Risk ve Etki Analizi
* **İş Sürekliliği:** Platformun çökmesi, tehdit görünürlüğünün (visibility) azalmasına ve saldırı tespit süresinin (MTTD) uzamasına neden olur.
* **Veri Güvenliği:** Platformun kendisi kritik istihbarat içerdiği için sızıntı durumunda saldırganlara "savunma stratejimizin haritasını" vermiş oluruz.

### 13. Sonuç
OpenCTima, modern siber savunma stratejilerinin (Proactive Defense) merkezinde yer alan kritik bir bileşendir. Doğru yapılandırıldığında, reaktif bir savunmadan proaktif, tehdit odaklı bir savunmaya geçişi sağlar.

---

*© 2025 AltHack Security — Gerçek Dünya Siber Güvenlik İstihbaratı*
