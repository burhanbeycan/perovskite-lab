# Donanım Gereksinimleri - Detaylı Spesifikasyonlar ve Maliyet Analizi

## Genel Bakış

Otonom perovskite lab sistemi, sentez, karakterizasyon ve optimizasyon için entegre donanım modüllerinden oluşur. Bu dokümanda tüm donanım bileşenleri, teknik spesifikasyonlar, tedarikçiler ve tahmini maliyetler detaylandırılmıştır.

---

## 1. Robotik Sentez Sistemi (ROSIE)

### 1.1. Robotik Kol

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | 6-DOF Endüstriyel Robot | Universal Robots | UR5e | $35,000 |
| **Yük Kapasitesi** | 5 kg | - | - | - |
| **Erişim Mesafesi** | 850 mm | - | - | - |
| **Tekrarlanabilirlik** | ±0.03 mm | - | - | - |
| **Kontrol** | Ethernet, USB | - | - | - |
| **Programlama** | Python SDK, URScript | - | - | - |
| **Güvenlik** | Collaborative (insan-robot işbirliği) | - | - | - |

**Alternatif Seçenekler:**
- **Bütçe Dostu**: Dobot CR5 ($15,000) - 5 kg yük, ±0.05 mm tekrarlanabilirlik
- **Yüksek Performans**: ABB IRB 1200 ($45,000) - 7 kg yük, ±0.01 mm tekrarlanabilirlik

### 1.2. Pipetleme Sistemi

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | 8-Kanal Elektronik Pipet | Eppendorf | E1-ClipTip 8 | $8,000 |
| **Hacim Aralığı** | 1-1000 µL | - | - | - |
| **Doğruluk** | ±1% (100 µL) | - | - | - |
| **Hassasiyet** | CV < 0.5% | - | - | - |
| **Kontrol** | USB, RS232 | - | - | - |
| **Uç Tipi** | Tek kullanımlık, steril | - | - | - |

**Ek Malzemeler:**
- Pipet uçları (1000 adet/kutu): $50/kutu × 10 = $500
- Uç kutuları ve raf sistemi: $300

### 1.3. Spin Coater

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Programlanabilir Spin Coater | Laurell Technologies | WS-650MZ-23NPPB | $12,000 |
| **Hız Aralığı** | 0-6000 rpm | - | - | - |
| **Hız Doğruluğu** | ±10 rpm | - | - | - |
| **İvmelenme** | 100-10000 rpm/s | - | - | - |
| **Substrat Boyutu** | Max 100 mm | - | - | - |
| **Vakum Chuck** | Dahil | - | - | - |
| **Kontrol** | RS232, Ethernet | - | - | - |

**Ek Özellikler:**
- Anti-solvent dispenser entegrasyonu
- Programlanabilir çoklu adım protokolleri
- Vakum pompası dahil

### 1.4. Hotplate (Tavlama İstasyonu)

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | 6-Pozisyon Programlanabilir Hotplate | IKA | RCT Digital 6 | $4,500 |
| **Sıcaklık Aralığı** | RT - 300°C | - | - | - |
| **Sıcaklık Doğruluğu** | ±0.5°C | - | - | - |
| **Homojenlik** | ±2°C (yüzey) | - | - | - |
| **Rampa Kontrolü** | 0.1-20°C/min | - | - | - |
| **Kontrol** | RS232, USB | - | - | - |
| **Güvenlik** | Aşırı ısınma koruması | - | - | - |

### 1.5. Karıştırma İstasyonu

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | 24-Pozisyon Manyetik Karıştırıcı | Heidolph | Multi Reax 24 | $3,500 |
| **Hız Aralığı** | 100-2000 rpm | - | - | - |
| **Vial Kapasitesi** | 2-50 mL | - | - | - |
| **Sıcaklık Kontrolü** | Opsiyonel (RT-80°C) | - | - | - |
| **Kontrol** | USB | - | - | - |

**Ek Malzemeler:**
- Cam vial'lar (2 mL, 100 adet): $200
- Manyetik karıştırıcı çubuklar (100 adet): $150
- Vial tutucular ve raflar: $400

### 1.6. Stok Çözelti Rafı

| Özellik | Spesifikasyon | Özel Yapım | Fiyat (USD) |
|---------|---------------|------------|-------------|
| **Pozisyon Sayısı** | 8 | - | $2,000 |
| **Hacim** | 50-250 mL şişeler | - | - |
| **Sıcaklık Kontrolü** | 10-25°C (Peltier) | - | - |
| **Işık Koruması** | Karanlık bölme | - | - |
| **Seviye Sensörü** | Kapasitif sensör (8×) | - | - |

### 1.7. Substrat Magazini

| Özellik | Spesifikasyon | Özel Yapım | Fiyat (USD) |
|---------|---------------|------------|-------------|
| **Kapasite** | 50 substrat | - | $1,500 |
| **Substrat Boyutu** | 25×25 mm | - | - |
| **Atmosfer** | N₂ purge | - | - |
| **Otomatik Besleme** | Robot erişimi | - | - |

### 1.8. Glovebox (İnert Atmosfer)

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | N₂ Glovebox | Inert Technology | PureLab HE 4GB | $45,000 |
| **Boyut** | 2000×1000×1200 mm | - | - | - |
| **O₂ Seviyesi** | < 1 ppm | - | - | - |
| **H₂O Seviyesi** | < 1 ppm | - | - | - |
| **Giriş** | Antechamber (2×) | - | - | - |
| **Kontrol** | Touchscreen, Ethernet | - | - | - |

**Alternatif:**
- **Bütçe Dostu**: Mbraun LABstar (< 10 ppm O₂/H₂O) - $25,000

---

## 2. Tier 1 Karakterizasyon Sistemi

### 2.1. PerovVision - Bilgisayarlı Görü

#### 2.1.1. Kamera Sistemi

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Sensör** | Sony IMX477, 12.3 MP | Raspberry Pi | HQ Camera | $50 |
| **Piksel Boyutu** | 1.55 µm | - | - | - |
| **Optik Format** | 1/2.3" | - | - | - |
| **Arayüz** | CSI (Raspberry Pi) / USB (adaptör) | - | - | - |

**Alternatif (Yüksek Performans):**
- FLIR Blackfly S (2.3 MP, USB3): $500

#### 2.1.2. Lens

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Odak Uzaklığı** | 16 mm | Computar | M1614-MP2 | $200 |
| **Diyafram** | f/1.4 | - | - | - |
| **Mount** | C-mount | - | - | - |
| **Çözünürlük** | 2 MP | - | - | - |

#### 2.1.3. Aydınlatma

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | LED Ring Light | Advanced Illumination | RL8365 | $300 |
| **Renk Sıcaklığı** | 5500K | - | - | - |
| **CRI** | > 95 | - | - | - |
| **Güç** | 12V, 10W | - | - | - |
| **Kontrol** | PWM dimming | - | - | - |

#### 2.1.4. Motorize Stage

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | XY Motorize Stage | Thorlabs | MLS203-1 | $3,500 |
| **Hareket Aralığı** | 25 mm (X, Y) | - | - | - |
| **Hassasiyet** | 0.1 µm | - | - | - |
| **Hız** | Max 2.5 mm/s | - | - | - |
| **Kontrol** | USB, Kinesis Software | - | - | - |

#### 2.1.5. Enclosure

| Özellik | Spesifikasyon | Özel Yapım | Fiyat (USD) |
|---------|---------------|------------|-------------|
| **Malzeme** | Alüminyum profil + siyah panel | - | $500 |
| **Boyut** | 500×500×600 mm | - | - |
| **İç Yüzey** | Mat siyah boya | - | - |
| **Erişim** | Kapak, robot girişi | - | - |

**PerovVision Toplam Maliyeti:** ~$5,050

### 2.2. PerImP - Fotolüminesans

#### 2.2.1. Uyarım Kaynağı

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | 405 nm LED | Thorlabs | M405L4 | $250 |
| **Güç** | 100 mW | - | - | - |
| **Dalga Boyu** | 405 ± 5 nm | - | - | - |
| **Işın Profili** | Gaussian | - | - | - |
| **Kontrol** | Akım sürücüsü (dahil) | - | - | - |

#### 2.2.2. Emisyon Filtresi

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Longpass Filter | Thorlabs | FEL0700 | $150 |
| **Geçiş Dalga Boyu** | 700 nm | - | - | - |
| **Boyut** | 25 mm çap | - | - | - |
| **Transmisyon** | > 90% (> 750 nm) | - | - | - |

#### 2.2.3. Spektrometre

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | USB Spektrometre | Ocean Insight | USB4000 | $3,500 |
| **Dalga Boyu Aralığı** | 200-1100 nm | - | - | - |
| **Optik Çözünürlük** | 1.5 nm (FWHM) | - | - | - |
| **Entegrasyon Süresi** | 3.8 ms - 10 s | - | - | - |
| **Arayüz** | USB 2.0 | - | - | - |

#### 2.2.4. PL Görüntüleme Kamerası

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Monochrome CMOS | FLIR | Blackfly S BFS-U3-23S3M | $500 |
| **Çözünürlük** | 1920×1200 (2.3 MP) | - | - | - |
| **Piksel Boyutu** | 5.86 µm | - | - | - |
| **Duyarlılık** | Yüksek (NIR) | - | - | - |
| **Arayüz** | USB 3.0 | - | - | - |

#### 2.2.5. Fiber Optik

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | UV-VIS Fiber | Ocean Insight | QP400-2-UV-VIS | $200 |
| **Core Çapı** | 400 µm | - | - | - |
| **Dalga Boyu Aralığı** | 200-1100 nm | - | - | - |
| **Uzunluk** | 2 m | - | - | - |

#### 2.2.6. Optik Bileşenler

| Bileşen | Spesifikasyon | Fiyat (USD) |
|---------|---------------|-------------|
| Lens (16 mm, f/1.4) | C-mount | $200 |
| Dichroic mirror (500 nm) | 25 mm | $300 |
| Optik tutucular ve montaj | Thorlabs | $400 |

**PerImP Toplam Maliyeti:** ~$5,500

### 2.3. Conductivity Module - 4-Nokta Prob

#### 2.3.1. 4-Point Probe Head

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | 4-Point Probe | Jandel Engineering | RM3000 | $4,000 |
| **Prob Aralığı** | 1.0 mm | - | - | - |
| **Prob Tipi** | Tungsten carbide | - | - | - |
| **Kuvvet** | Ayarlanabilir (10-100 g) | - | - | - |

#### 2.3.2. Source Meter

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Source Measure Unit | Keithley | 2450 | $8,500 |
| **Akım Aralığı** | 1 pA - 1 A | - | - | - |
| **Voltaj Aralığı** | ±200 V | - | - | - |
| **Çözünürlük** | 1 fA (akım), 1 µV (voltaj) | - | - | - |
| **Arayüz** | USB, GPIB, Ethernet | - | - | - |

**Alternatif (Bütçe Dostu):**
- Keithley 2400: $5,000 (±1 µA - 1 A)

#### 2.3.3. Z-Axis Stage

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Motorize Z-Stage | Thorlabs | Z825B | $2,000 |
| **Hareket Aralığı** | 25 mm | - | - | - |
| **Hassasiyet** | 0.05 µm | - | - | - |
| **Yük Kapasitesi** | 5 kg | - | - | - |
| **Kontrol** | USB | - | - | - |

#### 2.3.4. Force Sensor

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Loadcell | Futek | LSB200 | $500 |
| **Aralık** | 0-100 g | - | - | - |
| **Hassasiyet** | 0.01 g | - | - | - |
| **Çıkış** | Analog (amplifier dahil) | - | - | - |

**Conductivity Module Toplam Maliyeti:** ~$15,000

---

## 3. Tier 2 Karakterizasyon Sistemi

### 3.1. XRD (X-Ray Diffraction)

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Benchtop XRD | Bruker | D2 PHASER | $85,000 |
| **Kaynak** | Cu Kα (λ = 1.5406 Å) | - | - | - |
| **Dedektör** | LYNXEYE XE-T | - | - | - |
| **2θ Aralığı** | 5-90° | - | - | - |
| **Çözünürlük** | 0.01° | - | - | - |
| **Otomatik Örnek Değiştirici** | 10 pozisyon | - | - | - |
| **Yazılım** | DIFFRAC.SUITE | - | - | - |

**Alternatif (Bütçe Dostu):**
- Rigaku MiniFlex 600: $65,000

### 3.2. UV-Vis Spektrofotometre

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | UV-Vis-NIR | PerkinElmer | Lambda 365 | $25,000 |
| **Dalga Boyu Aralığı** | 190-1100 nm | - | - | - |
| **Spektral Bant Genişliği** | 1 nm | - | - | - |
| **Mod** | Transmittans, Absorbans | - | - | - |
| **Otomasy on** | 6-pozisyon örnek tutucu | - | - | - |

**Alternatif:**
- Agilent Cary 60: $18,000

### 3.3. Profilometre

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Tip** | Stylus Profilometer | Bruker | DektakXT | $45,000 |
| **Dikey Çözünürlük** | 1 nm | - | - | - |
| **Tarama Uzunluğu** | 55 mm | - | - | - |
| **Kuvvet Aralığı** | 1-15 mg | - | - | - |
| **Otomatik Stage** | XY motorize | - | - | - |

**Alternatif:**
- KLA Tencor P-7: $35,000

---

## 4. Kontrol ve Bilgisayar Sistemi

### 4.1. Ana Kontrol Bilgisayarı

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **İşlemci** | Intel Core i9-13900K (24-core) | Custom Build | - | $3,500 |
| **RAM** | 64 GB DDR5 | - | - | - |
| **Depolama** | 2 TB NVMe SSD + 8 TB HDD | - | - | - |
| **GPU** | NVIDIA RTX 4070 (ML için) | - | - | - |
| **İşletim Sistemi** | Ubuntu 22.04 LTS | - | - | - |
| **Arayüzler** | 8× USB 3.0, 2× USB-C, 4× RS232, 2× GPIB, Ethernet | - | - | - |

### 4.2. Yedek Güç Kaynağı (UPS)

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Kapasite** | 3000 VA / 2700 W | APC | Smart-UPS 3000 | $1,500 |
| **Yedekleme Süresi** | 15-30 dakika (tam yük) | - | - | - |
| **Arayüz** | USB, Ethernet | - | - | - |

### 4.3. Network Attached Storage (NAS)

| Özellik | Spesifikasyon | Tedarikçi | Model | Fiyat (USD) |
|---------|---------------|-----------|-------|-------------|
| **Kapasite** | 24 TB (4× 6TB RAID 5) | Synology | DS920+ | $2,500 |
| **Arayüz** | Gigabit Ethernet | - | - | - |
| **Yedekleme** | Otomatik, bulut senkronizasyonu | - | - | - |

---

## 5. Yardımcı Ekipmanlar

### 5.1. Kimyasal Saklama ve Güvenlik

| Ekipman | Spesifikasyon | Fiyat (USD) |
|---------|---------------|-------------|
| Kimyasal dolabı (yangın dayanımlı) | 45 galon | $1,200 |
| Aspiratör (fume hood) | 4 ft genişlik | $8,000 |
| Yangın söndürücü (CO₂) | 5 kg | $150 |
| İlk yardım kiti | Kimyasal maruziyeti | $200 |
| Kişisel koruyucu ekipman | Eldiven, gözlük, önlük | $300 |

### 5.2. Laboratuvar Mobilyası

| Ekipman | Spesifikasyon | Fiyat (USD) |
|---------|---------------|-------------|
| Laboratuvar masası (3×) | 2 m × 0.75 m, kimyasal dayanımlı | $3,000 |
| Titreşim izolasyon masası | Optik ekipman için | $2,500 |
| Depolama rafları | Metal, ayarlanabilir | $800 |
| Sandalyeler (3×) | Ergonomik, ayarlanabilir | $600 |

### 5.3. Küçük Ekipmanlar

| Ekipman | Spesifikasyon | Fiyat (USD) |
|---------|---------------|-------------|
| Analitik terazi | 0.1 mg hassasiyet | $2,500 |
| pH metre | Dijital, kalibrasyon sertifikalı | $500 |
| Ultrasonik temizleyici | 3 L | $400 |
| UV-Ozon temizleyici | Substrat temizleme | $2,000 |
| Vakum pompası | Spin coater için | $800 |
| N₂ jeneratörü | Glovebox besleme | $5,000 |
| Sıvı azot dewar | 50 L | $1,000 |

---

## 6. Yazılım ve Lisanslar

| Yazılım | Amaç | Lisans Tipi | Fiyat (USD/yıl) |
|---------|------|-------------|-----------------|
| Python + Kütüphaneler | Ana kontrol yazılımı | Açık kaynak | $0 |
| LabVIEW | Cihaz kontrol (opsiyonel) | Ticari | $2,500 |
| MATLAB | Veri analizi | Akademik | $500 |
| Origin Pro | Grafik ve analiz | Ticari | $1,200 |
| PostgreSQL | Veritabanı | Açık kaynak | $0 |
| Redis | Cache & Queue | Açık kaynak | $0 |
| Node.js + React | Web arayüzü | Açık kaynak | $0 |
| PyTorch / TensorFlow | ML framework | Açık kaynak | $0 |
| GPy / BoTorch | Bayesian optimization | Açık kaynak | $0 |

**Yazılım Toplam (İlk Yıl):** ~$4,200

---

## 7. Maliyet Özeti

### 7.1. Kategori Bazında Maliyet

| Kategori | Maliyet (USD) | Yüzde |
|----------|---------------|-------|
| **Robotik Sentez Sistemi** | $112,200 | 37% |
| - Robotik kol | $35,000 | |
| - Pipetleme | $8,800 | |
| - Spin coater | $12,000 | |
| - Hotplate | $4,500 | |
| - Karıştırma | $4,050 | |
| - Stok rafı | $2,000 | |
| - Substrat magazini | $1,500 | |
| - Glovebox | $45,000 | |
| **Tier 1 Karakterizasyon** | $25,550 | 8% |
| - PerovVision | $5,050 | |
| - PerImP | $5,500 | |
| - Conductivity | $15,000 | |
| **Tier 2 Karakterizasyon** | $155,000 | 51% |
| - XRD | $85,000 | |
| - UV-Vis | $25,000 | |
| - Profilometre | $45,000 | |
| **Bilgisayar ve IT** | $7,500 | 2% |
| - Ana bilgisayar | $3,500 | |
| - UPS | $1,500 | |
| - NAS | $2,500 | |
| **Yardımcı Ekipmanlar** | $28,450 | 9% |
| - Güvenlik | $9,850 | |
| - Mobilya | $6,900 | |
| - Küçük ekipmanlar | $11,700 | |
| **Yazılım (İlk Yıl)** | $4,200 | 1% |
| **TOPLAM** | **$332,900** | **100%** |

### 7.2. Aşamalı Kurulum Seçenekleri

#### Seçenek A: Minimal Başlangıç (Tier 1 Odaklı)

| Kategori | Maliyet (USD) |
|----------|---------------|
| Robotik sentez (basitleştirilmiş) | $75,000 |
| - Dobot CR5 (robot) | $15,000 |
| - Pipet | $8,800 |
| - Spin coater | $12,000 |
| - Hotplate | $4,500 |
| - Karıştırma | $4,050 |
| - Basit glovebox | $25,000 |
| - Diğer | $5,650 |
| Tier 1 karakterizasyon | $25,550 |
| Bilgisayar | $7,500 |
| Yardımcı ekipmanlar | $15,000 |
| **TOPLAM** | **$123,050** |

**Avantajlar:**
- Düşük başlangıç maliyeti
- Hızlı kurulum (2-3 ay)
- Tier 1 ile yüksek throughput

**Dezavantajlar:**
- Detaylı karakterizasyon için dış hizmet gerekli
- Bant aralığı ve kristallik ölçümü sınırlı

#### Seçenek B: Tam Sistem

| Kategori | Maliyet (USD) |
|----------|---------------|
| Tüm bileşenler | $332,900 |

**Avantajlar:**
- Tam otonom sistem
- Tüm karakterizasyon in-house
- Maksimum throughput ve veri kalitesi

**Dezavantajlar:**
- Yüksek başlangıç maliyeti
- Uzun kurulum süresi (6-9 ay)
- Daha fazla bakım ve operasyon

#### Seçenek C: Hibrit (Tier 1 + Seçilmiş Tier 2)

| Kategori | Maliyet (USD) |
|----------|---------------|
| Robotik sentez (tam) | $112,200 |
| Tier 1 karakterizasyon | $25,550 |
| UV-Vis (Tier 2) | $25,000 |
| Profilometre (Tier 2) | $45,000 |
| Bilgisayar | $7,500 |
| Yardımcı ekipmanlar | $28,450 |
| **TOPLAM** | **$243,700** |

**Avantajlar:**
- Dengeli maliyet/performans
- Film kalınlığı ve bant aralığı ölçümü in-house
- XRD için dış hizmet kullanımı

---

## 8. İşletme Maliyetleri (Yıllık)

| Kategori | Maliyet (USD/yıl) |
|----------|-------------------|
| **Sarf Malzemeleri** | |
| - Pipet uçları | $2,000 |
| - Kimyasallar (prekursorler) | $15,000 |
| - Substratlar (ITO cam) | $5,000 |
| - N₂ gazı | $3,000 |
| - Temizlik kimyasalları | $1,000 |
| **Bakım ve Kalibrasyon** | |
| - Robot servisi | $3,000 |
| - Cihaz kalibrasyonları | $5,000 |
| - XRD tüp değişimi (5 yılda 1) | $2,000 |
| **Yazılım Lisansları** | $4,200 |
| **Elektrik** | $5,000 |
| **Personel (1 teknisyen)** | $50,000 |
| **TOPLAM** | **$95,200** |

---

## 9. Tedarikçi Bilgileri

### 9.1. Ana Tedarikçiler

| Tedarikçi | Ürün Kategorisi | Website | İletişim |
|-----------|-----------------|---------|----------|
| Universal Robots | Robotik kol | www.universal-robots.com | sales@universal-robots.com |
| Eppendorf | Pipetleme | www.eppendorf.com | info@eppendorf.com |
| Laurell Technologies | Spin coater | www.laurell.com | sales@laurell.com |
| Bruker | XRD, Profilometre | www.bruker.com | info@bruker.com |
| Keithley (Tektronix) | Source meter | www.tek.com/keithley | keithley@tek.com |
| Thorlabs | Optik bileşenler | www.thorlabs.com | sales@thorlabs.com |
| Ocean Insight | Spektrometre | www.oceaninsight.com | info@oceaninsight.com |
| Inert Technology | Glovebox | www.inert-corp.com | sales@inert-corp.com |

### 9.2. Satın Alma Önerileri

1. **Fiyat Teklifi Alın**: Tüm büyük ekipmanlar için en az 3 tedarikçiden teklif alın
2. **Akademik İndirim**: Üniversite bağlantısı varsa %10-20 indirim mümkün
3. **Paket Anlaşma**: Aynı tedarikçiden çoklu ürün alımında indirim talep edin
4. **Garanti ve Servis**: Minimum 2 yıl garanti ve yerinde servis şartı ekleyin
5. **Eğitim**: Kurulum ve operasyon eğitimi dahil olmalı
6. **Teslimat Süresi**: Özellikle glovebox ve XRD için 3-6 ay teslimat süresi planlayın

---

## 10. Kurulum Zaman Çizelgesi

| Ay | Aktivite |
|----|----------|
| **1-2** | Sipariş verme, laboratuvar hazırlığı (elektrik, gaz hatları) |
| **3-4** | Temel ekipman teslimatı (robot, spin coater, hotplate) |
| **4-5** | Glovebox kurulumu ve komisyonu |
| **5-6** | Tier 1 karakterizasyon kurulumu |
| **6-7** | Yazılım entegrasyonu ve test |
| **7-8** | Tier 2 ekipman teslimatı (XRD, UV-Vis) |
| **8-9** | Tam sistem entegrasyonu |
| **9-10** | Kalibrasyon ve validasyon |
| **10-12** | Operasyonel testler ve optimizasyon |

**Toplam Kurulum Süresi:** 10-12 ay (tam sistem)

Bu detaylı donanım listesi, otonom perovskite lab sisteminin kurulumu için eksiksiz bir rehber sağlar.
