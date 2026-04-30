# MCP-Sunucuları ile AI-Destekli CTI

## 1. Genel Bakış 
* **CTI Konusu:** MCP-Sunucuları ile AI-Destekli CTI Mimarileri ve Güvenlik İmplikasyonları
* **Kategori:** Emerging Technology / New Attack Surface / AI Security
* **Durum:** Emerging (Gelişmekte olan teknoloji ve yeni risk alanı)

## 2. Teknik Özet
Model Context Protocol (MCP), Büyük Dil Modellerinin (LLM) harici veri kaynaklarına (dosyalar, veritabanları, API'lar) ve araçlara (tools) standart bir şekilde erişmesini sağlayan açık bir protokoldür. CTI süreçlerinde bu teknoloji, dağınık güvenlik verilerini (SIEM, EDR, Threat Intel Feed) tek bir yapay zeka ajanı altında toplama potansiyeline sahiptir. Ancak, bu mimari; **Indirect Prompt Injection**, **Unauthorized Tool Execution** ve **Data Exfiltration via LLM Context** gibi yeni nesil tehdit vektörlerini beraberinde getirmektedir.

## 3. Tehdit Tanımı ve Problem
**Problem:** Geleneksel CTI süreçleri, verinin çok çeşitli kaynaklarda (fragmented) olması nedeniyle yavaştır. AI-Destekli CTI, bu veriyi hızlandırmak için MCP kullanarak "Agentic" (eylem alabilen) yapılar kurmaktadır.
**Tehdit:** Bir saldırganın, MCP sunucularına erişebilen bir LLM ajanı üzerinden, dolaylı komut enjeksiyonu (Indirect Prompt Injection) yoluyla kritik güvenlik altyapılarına (örneğin bir EDR'a komut gönderme veya hassas logları okuma) yetkisiz erişim sağlamasıdır.

**Hedeflenen Varlıklar:**
* SOC Analistlerinin kullandığı AI Asistanları.
* MCP Sunucuları (Local veya Remote).
* Entegre edilmiş güvenlik araçları (SIEM, SOAR, EDR, Cloud Logs).

## 4. Etkilenen Sistemler ve Cihazlar
* **AI Orchestrators:** LLM tabanlı karar verici mekanizmalar.
* **MCP Servers:** Veri kaynağı ve araç sağlayıcı sunucular (Python/Node.js tabanlı).
* **Security Infrastructure:** Endpoint, Network ve Cloud ortamlarındaki API uç noktaları.
* **Data Lakes/SIEM:** Analiz edilen ham veri kaynakları.

## 5. Teknik Detaylar
MCP mimarisi üç ana bileşenden oluşur: **Host (LLM Client), Client, ve Server**.

### MITRE ATT&CK Eşleştirmeleri 
| Teknik | MITRE ID | Açıklama |
| :--- | :--- | :--- |
| **Indirect Prompt Injection** | *New/Emerging* | Saldırganın, MCP aracılığıyla okunan bir dokümana (örn. bir phishing maili) gizli talimatlar yerleştirerek AI ajanını manipüle etmesi. |
| **Unauthorized Tool Use** | T1566 (Mod) | AI ajanının, yetki dışı bir MCP tool'unu (örn. `delete_log_entry`) çalıştırması. |
| **Data Exfiltration** | T1041 | AI ajanı aracılığıyla, MCP üzerinden erişilen hassas verilerin LLM context'ine aktarılıp dışarı sızdırılması. |

## 6. Saldırı Zinciri 
1.  **Initial Access:** Saldırganın, MCP sunucusunun okuduğu bir veri kaynağına (doküman, web sayfası, email) zararlı talimat yerleştirmesi.
2.  **Execution (Prompt Injection):** AI ajanı, MCP üzerinden bu veriyi okuduğunda, talimatı "sistem komutu" olarak algılar.
3.  **Persistence:** AI ajanı üzerinden MCP araçlarını kullanarak sistemde yeni yetkili kullanıcılar veya API key'ler oluşturulması.
4.  **Defense Evasion:** AI ajanı aracılığıyla, güvenlik loglarının (SIEM) MCP üzerinden manipüle edilmesi veya temizlenmesi.
5.  **Exfiltration:** Hassas verilerin, AI yanıtları (responses) içine gömülerek saldırganın izleyebileceği bir kanala (örn. bir webhook) aktarılması.

## 7. Tehdit Aktörü
* **Profil:** Gelişmiş Tehdit Aktörleri (APT) ve AI-Automated Malware geliştiricileri.
* **Motivasyon:** Casusluk (Espionage), Veri Hırsızlığı, Güvenlik Altyapısını Felç Etme.
* **TTP'ler:** Prompt Engineering, Automated Reconnaissance, Tool-Use Exploitation.

## 8. IoC 
*MCP mimarisi için IoC'ler geleneksel hash'lerden ziyade davranışsal izlerdir:*
* **Anomalous Tool Calls:** Bir AI ajanı tarafından normal dışı frekansta veya sıklıkta çağrılan MCP tool'ları.
* **Context Overflow/Manipulation:** LLM sistem mesajlarında (system prompt) ani ve açıklanamayan değişiklikler.
* **Unexpected Outbound Traffic:** MCP sunucularından veya AI Host'tan, tanımlanmamış dış IP/Domain'lere giden API istekleri.
* **Large Context Windows:** Bir seferde aşırı miktarda veri okumaya çalışan (exfiltration belirtisi) MCP sorguları.

## 9. Detection 
* **SIEM Use-Case:** MCP sunucu loglarında "Tool Call" başarısızlıklarının ve yetkisiz erişim denemelerinin izlenmesi.
* **EDR/XDR Detection:** MCP sunucusunu çalıştıran process'lerin (Python, Node.js) ağ aktivitelerindeki anomalilerin takibi.
* **LLM Guardrails:** Girdi ve çıktıların (Input/Output) içerdiği "malicious instruction" paternlerinin (Regex, NLP tabanlı) tespiti.
* **Audit Logs:** MCP sunucularının hangi veri kaynaklarına (Files, DB, API) eriştiğinin immutable loglarının tutulması.

## 10. Mitigation & Defense
* **Kısa Vadeli:**
    * **Least Privilege:** Her MCP sunucusuna sadece ihtiyaç duyduğu minimum veri setine erişim yetkisi verilmesi.
    * **Human-in-the-loop (HITL):** Kritik "Write" veya "Delete" işlemlerinde AI ajanının onay mekanizmasına tabi tutulması.
* **Uzun Vadeli:**
    * **Sandboxing:** MCP sunucularının izole edilmiş (containerized) ortamlarda çalıştırılması.
    * **Strict Schema Validation:** MCP tool çıktıları ve girdileri için katı veri tipi ve şema kontrolü.
    * **Prompt Sanitization:** Girdi verilerinin, ajanı manipüle etmesini önleyecek şekilde temizlenmesi.

## 11. Incident Response
1.  **Containment:** Etkilenen MCP sunucusunun ağ erişiminin kesilmesi ve AI ajanı oturumunun sonlandırılması.
2.  **Eradication:** Zararlı talimat içeren veri kaynaklarının (dokümanlar, mail trafiği) temizlenmesi; MCP konfigürasyonlarının geri yüklenmesi.
lama.
3.  **Recovery:** Güvenli bir snapshot üzerinden MCP servislerinin ayağa kaldırılması ve yetki denetimi (IAM audit).

## 12. Risk ve Etki Analizi
* **İş Sürekliliği:** Yüksek (Güvenlik otomasyonunun devre dışı kalması operasyonel körlüğe yol açabilir).
* **Veri Güvenliği:** Kritik (Hassas siber güvenlik verilerinin ve kurumsal sırların sızma riski).
* **Kurumsal Risk:** Yüksek (AI tabanlı karar mekanizmalarının güven kaybı, tüm savunma hattını etkiler).

## 13. İstihbarat Değeri
Bu analiz, organizasyonun **AI Güvenlik Stratejisi** için temel teşkil eder. SOC ekiplerinin, sadece geleneksel malware'lere değil, **Agentic AI** ekosistemindeki yeni saldırı vektörlerine karşı hazırlıklı olmasını sağlar. Threat Hunting ekipleri için MCP loglarını yeni bir "hunting ground" (av sahası) olarak tanımlar.

## 14. Sonuç ve Değerlendirme
MCP-Sunucuları ile AI-Destekli CTI, savunma tarafında devrim niteliğinde bir yetenek sunsa da, "untrusted input" (güvenilmeyen girdi) riskini kritik seviyeye çıkarmaktadır. Organizasyonlar, bu teknolojiyi benimserken **"Zero Trust"** prensibini MCP mimarisine de uygulamalıdır.

---

*© 2025 AltHack Security — Gerçek Dünya Siber Güvenlik İstihbaratı*
