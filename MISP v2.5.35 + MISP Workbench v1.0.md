# MISP v2.5.35 + MISP Workbench v1.0 — Tehdit Paylaşım Ekosistemi Yeniden Şekilleniyor

**Tarih:** 19 Mart 2026 (v2.5.35) | 13 Mart 2026 (Workbench v1.0 beta)  
**Platform:** MISP (Malware Information Sharing Platform)  
**Kategori:** Threat Intelligence Sharing Platform  
**Lisans:** AGPL v3  
**Durum:** Aktif Geliştirme — Production Ready ✔

## Özet

MISP, dünya genelinde 10.000’den fazla kurumun aktif olarak kullandığı, tehdit istihbaratı paylaşımının fiili standardı haline gelmiş açık kaynak bir platformdur. 

Mart 2026’da iki kritik gelişme yaşandı:  
- **MISP v2.5.35** ile platformun mimarisi derinlemesine yenilendi.  
- Aynı zamanda büyük ölçekli analiz için tasarlanmış bağımsız bir araç olan **MISP Workbench v1.0** duyuruldu.

## Teknik Yenilikler — MISP v2.5.35

### 1. Ayrıştırılmış Event View Mimarisi (Decomposed Event Views)

v2.5.35 sürümü, Event View üzerinde önemli mimari iyileştirmeler getirdi. Yeni endpoint’ler:
- `view2()` (metadata shell)
- `viewAttributes()`
- `viewObjects()`
- `viewRelatedEvents()`
- `viewWarninglistHits()`

EventTest Teması ile birlikte sayfalandırılmış özellikler, hızlı filtreler ve düzleştirme toggle’ı sunan yeni bir referans UI uygulaması yayınlandı.

**Teknik önemi:**  
Önceki monolitik `view()` yapısı, binlerce IoC içeren büyük olaylarda tarayıcıyı kilitleyebiliyordu. Yeni AJAX tabanlı ayrıştırılmış mimari, bileşenlerin asenkron yüklenmesini sağlayarak SOC ortamlarında ciddi performans kazancı sunuyor.

### 2. MISP-STIX 2026.3.13 — Çift Yönlü Dönüşüm Güçlendi

Öne çıkan yenilikler:
- Yeni gösterge-gözlemlenebilir parmak izi sistemi
- Çift gösterge + gözlemlenen veri dışa aktarımı
- Genişletilmiş MISP nesne desteği
- ACS işaret tanımı yönetimi

**Pratik etkisi:**  
`to_ids=True` bayrağının MISP → OpenCTI → MISP dönüşüm zincirlerinde kaybolması sorunu çözüldü. Bu sayede veri bütünlüğü korunuyor ve yanlış pozitif oranı azalıyor.

### 3. Güvenlik Sertleştirmeleri

- Yönlendirme Koruması (Dashboard widget’larından yetkisiz harici yönlendirmelerin engellenmesi)
- Dosya Yükleme Doğrulaması
- Authkey oluşturma ve depolama iyileştirmeleri
- URL Geçişi ayarları

**Düzeltilen Güvenlik Açıkları:**
- GCVE-1-2026-0016 — Event Report Import’ta SSRF
- GCVE-1-2026-0018 — Kullanıcı iletişim formunda kurumlar arası e-posta hedefleme
- GCVE-1-2026-0019 — Dashboard widget’ında dış yönlendirme

## Teknik Yenilikler — MISP Workbench v1.0 Beta

MISP Workbench, birden fazla MISP örneği, harici beslemeler ve diğer tehdit istihbaratı kaynaklarından gelen verileri tek bir birleşik çalışma alanında toplayan güçlü bir analist odaklı platformdur.

**Temel Kullanım Senaryoları:**
- **Büyük ölçekte gösterge triajı**
- **Tehdit avcılığı** (kayıtlı aramalar ve otomatik uyarılar)
- **Besleme konsolidasyonu ve tekilleştirme**                        

## Drone İstihbaratı: Yeni Boyut
MISP’e entegre edilen yeni UAV (İnsansız Hava Aracı) ve kontrolcü nesneleri eklendi.
Desteklenen alanlar: üretici, model, varyant, seri numarası, kayıt kimliği, MAC adresi, uçuş rotası, firmware sürümü/hash vb.
Bu özellik özellikle OT/ICS ve fiziksel güvenlik ekipleri için önemli bir yenilik.

## CTI Analisti İçin Pratik Değer

| Senaryo                        | Öncesi                          | Sonrası                               |
|--------------------------------|---------------------------------|---------------------------------------|
| Büyük event yükleme            | Tarayıcı kilitleniyor           | AJAX asenkron yükleme                 |
| STIX dışa aktarım              | to_ids kaybı                    | Parmak izi sistemi + round-trip       |
| Çoklu besleme triajı           | Manuel, araç sıçraması          | Workbench birleşik arayüzü            |
| Dashboard URL’sü               | Açık yönlendirme                | Korumalı URL geçişi                   |
| Drone olayı modelleme          | Yapısız metin                   | UAV nesne + taxonomi                  |

## Hızlı Kurulunm
```bash
# MISP v2.5.35
git clone https://github.com/MISP/MISP
cd MISP

# Docker ile önerilen kurulum
docker pull misp/misp

# MISP Workbench
git clone https://github.com/MISP/misp-workbench
cd misp-workbench
pip install -r requirements.txt
python workbench.py --config config.yaml
```

## Kaynaklar

| # | Kaynak                                   | Tür                  | URL |
|---|------------------------------------------|----------------------|-----|
| 1 | GitHub — MISP Core                       | Birincil             | [https://github.com/MISP/MISP](https://github.com/MISP/MISP) |
| 2 | MISP v2.5.35 Release Notes               | Birincil             | [https://www.misp-project.org/2026/03/19/misp.2.5.35.released.html/](https://www.misp-project.org/2026/03/19/misp.2.5.35.released.html/) |
| 3 | MISP Workbench v1.0 Beta                 | Ürün Duyurusu        | [https://www.misp-project.org/2026/03/13/misp-workbench_beta_1.0_released.html/](https://www.misp-project.org/2026/03/13/misp-workbench_beta_1.0_released.html/) |
| 4 | MISP-STIX 2026.3.13                      | Teknik Blog          | [https://www.misp-project.org/2026/03/16/misp-stix_indicator_and_observable_fingerprinting.html/](https://www.misp-project.org/2026/03/16/misp-stix_indicator_and_observable_fingerprinting.html/) |
| 5 | MISP v2.5.33 Güvenlik Notları            | Changelog            | [https://www.misp-project.org/2026/03/03/misp.2.5.33.released.html/](https://www.misp-project.org/2026/03/03/misp.2.5.33.released.html/) |
| 6 | MISP Drone İstihbaratı                   | Araştırma            | [https://www.misp-project.org/2026/03/10/have-you-ever-thought-about-drones-in-misp.html/](https://www.misp-project.org/2026/03/10/have-you-ever-thought-about-drones-in-misp.html/) |
| 7 | MISP Proje Ana Sayfası                   | Genel                | [https://www.misp-project.org/](https://www.misp-project.org/) |

---

© 2025 AltHack Security — Gerçek Dünya Siber Güvenlik İstihbaratı
