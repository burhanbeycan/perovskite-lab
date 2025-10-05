# Tier 1 Karakterizasyon Modülleri - Detaylı Teknik Dokümantasyon

## Genel Bakış

Tier 1 karakterizasyon sistemi, perovskite ince filmlerin ultra-hızlı (< 2 dakika/örnek) ve tahribatsız analizini sağlayan üç entegre modülden oluşur. Bu modüller, makine öğrenmesi destekli optimizasyon döngüsüne gerçek zamanlı veri akışı sağlar.

## 1. PerovVision: Bilgisayarlı Görü Modülü

### 1.1. Sistem Mimarisi

PerovVision, yüksek çözünürlüklü görüntü analizi ile perovskite filmlerin morfolojik ve optik özelliklerini piksel seviyesinde değerlendirir.

#### Donanım Bileşenleri

| Bileşen | Spesifikasyon | Amaç |
|---------|---------------|------|
| **Kamera** | Sony IMX477, 12.3 MP, 1/2.3" sensor | Yüksek çözünürlüklü görüntü yakalama |
| **Lens** | 16mm f/1.4, C-mount | Düşük distorsiyon, yüksek kontrast |
| **Aydınlatma** | LED ring light, 5500K, CRI>95 | Homojen, renk doğru aydınlatma |
| **Motorize Stage** | XY stage, 0.1 µm hassasiyet | Çoklu pozisyon taraması |
| **Enclosure** | Karanlık kutu, mat siyah iç yüzey | Dış ışık ve yansıma eliminasyonu |

#### Yazılım Pipeline

```python
# PerovVision İşleme Pipeline'ı

class PerovVision:
    def __init__(self):
        self.camera = Camera(resolution=(4056, 3040))
        self.preprocessor = ImagePreprocessor()
        self.segmentation_model = load_model('perovskite_segmentation.h5')
        self.feature_extractor = FeatureExtractor()
        
    def capture_image(self, sample_id):
        """
        Örnek görüntüsünü yakala
        """
        # Kamera ayarları
        self.camera.set_exposure(50)  # ms
        self.camera.set_gain(1.0)
        self.camera.set_white_balance('auto')
        
        # Çoklu pozisyon taraması (3x3 grid)
        images = []
        for x in range(3):
            for y in range(3):
                self.stage.move_to(x * 5, y * 5)  # 5mm aralık
                time.sleep(0.1)  # Stabilizasyon
                img = self.camera.capture()
                images.append(img)
        
        # Görüntüleri birleştir (stitching)
        full_image = self.stitch_images(images)
        return full_image
    
    def preprocess(self, image):
        """
        Görüntü ön işleme
        """
        # 1. Renk düzeltme
        image = self.preprocessor.color_correction(image)
        
        # 2. Gürültü azaltma (bilateral filter)
        image = cv2.bilateralFilter(image, d=5, sigmaColor=75, sigmaSpace=75)
        
        # 3. Kontrast normalizasyonu (CLAHE)
        lab = cv2.cvtColor(image, cv2.COLOR_RGB2LAB)
        l, a, b = cv2.split(lab)
        clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8,8))
        l = clahe.apply(l)
        image = cv2.merge([l, a, b])
        image = cv2.cvtColor(image, cv2.COLOR_LAB2RGB)
        
        return image
    
    def segment_film(self, image):
        """
        Film bölgelerini segmente et
        """
        # U-Net tabanlı semantik segmentasyon
        mask = self.segmentation_model.predict(image)
        
        # Sınıflar:
        # 0: Background (substrat)
        # 1: Perovskite film
        # 2: Pinholes
        # 3: Cracks
        # 4: Aggregates
        
        return mask
    
    def extract_features(self, image, mask):
        """
        Kantitatif özellikler çıkar
        """
        features = {}
        
        # 1. Kaplama Oranı (Coverage)
        total_pixels = mask.shape[0] * mask.shape[1]
        film_pixels = np.sum(mask == 1)
        features['coverage'] = film_pixels / total_pixels
        
        # 2. Kusur Yoğunluğu (Defect Density)
        pinhole_pixels = np.sum(mask == 2)
        crack_pixels = np.sum(mask == 3)
        defect_area = (pinhole_pixels + crack_pixels) * (pixel_size ** 2)  # mm²
        features['defect_density'] = defect_area / film_area  # defects/cm²
        
        # 3. Homojenlik (Homogeneity)
        # RGB kanallarının standart sapması
        rgb_std = np.std(image[mask == 1], axis=0)
        features['homogeneity'] = 1 - (np.mean(rgb_std) / 255.0)
        
        # 4. Renk Özellikleri
        mean_rgb = np.mean(image[mask == 1], axis=0)
        features['mean_R'] = mean_rgb[0]
        features['mean_G'] = mean_rgb[1]
        features['mean_B'] = mean_rgb[2]
        
        # 5. Tekstür Özellikleri (GLCM)
        gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)
        glcm = graycomatrix(gray[mask == 1], [1], [0, np.pi/4, np.pi/2, 3*np.pi/4])
        features['contrast'] = graycoprops(glcm, 'contrast').mean()
        features['correlation'] = graycoprops(glcm, 'correlation').mean()
        features['energy'] = graycoprops(glcm, 'energy').mean()
        features['homogeneity_glcm'] = graycoprops(glcm, 'homogeneity').mean()
        
        # 6. Kristal Boyutu Tahmini (Fourier analizi)
        fft = np.fft.fft2(gray[mask == 1])
        fft_shift = np.fft.fftshift(fft)
        magnitude = np.abs(fft_shift)
        features['crystal_size_estimate'] = self.estimate_crystal_size(magnitude)
        
        return features
    
    def calculate_quality_score(self, features):
        """
        Genel kalite skoru hesapla (0-1 arası)
        """
        # Ağırlıklı skor
        Q = (
            0.3 * features['coverage'] +
            0.3 * features['homogeneity'] +
            0.2 * (1 - features['defect_density'] / 100) +  # normalize
            0.2 * features['correlation']
        )
        return np.clip(Q, 0, 1)
    
    def analyze(self, sample_id):
        """
        Tam analiz pipeline'ı
        """
        # 1. Görüntü yakalama
        image = self.capture_image(sample_id)
        
        # 2. Ön işleme
        processed = self.preprocess(image)
        
        # 3. Segmentasyon
        mask = self.segment_film(processed)
        
        # 4. Özellik çıkarımı
        features = self.extract_features(processed, mask)
        
        # 5. Kalite skoru
        quality_score = self.calculate_quality_score(features)
        
        # 6. Görselleştirme
        visualization = self.create_visualization(image, mask, features)
        
        # 7. Sonuçları kaydet
        results = {
            'sample_id': sample_id,
            'timestamp': datetime.now().isoformat(),
            'features': features,
            'quality_score': quality_score,
            'raw_image_path': f'data/images/{sample_id}_raw.png',
            'mask_path': f'data/images/{sample_id}_mask.png',
            'viz_path': f'data/images/{sample_id}_viz.png'
        }
        
        # Görüntüleri kaydet
        cv2.imwrite(results['raw_image_path'], image)
        cv2.imwrite(results['mask_path'], mask * 50)  # Görünürlük için ölçekle
        cv2.imwrite(results['viz_path'], visualization)
        
        return results
```

### 1.2. Performans Metrikleri

- **İşlem Süresi**: 45-60 saniye/örnek
  - Görüntü yakalama: 15 s
  - Ön işleme: 5 s
  - Segmentasyon: 20 s
  - Özellik çıkarımı: 10 s
  - Kaydetme: 5 s

- **Doğruluk**: 
  - Segmentasyon IoU: 0.92 ± 0.03
  - Kaplama oranı hatası: < 2%
  - Kusur tespiti F1-score: 0.88

### 1.3. Çıktı Formatı

```json
{
  "sample_id": "EXP_158",
  "timestamp": "2025-10-04T19:30:45",
  "features": {
    "coverage": 0.952,
    "defect_density": 12.3,
    "homogeneity": 0.876,
    "mean_R": 187.5,
    "mean_G": 142.3,
    "mean_B": 89.7,
    "contrast": 45.2,
    "correlation": 0.823,
    "energy": 0.156,
    "crystal_size_estimate": 245.0
  },
  "quality_score": 0.891,
  "raw_image_path": "data/images/EXP_158_raw.png",
  "mask_path": "data/images/EXP_158_mask.png",
  "viz_path": "data/images/EXP_158_viz.png"
}
```

---

## 2. PerImP: Perovskite Image Processing & Photoluminescence

### 2.1. Sistem Mimarisi

PerImP modülü, fotolüminesans görüntüleme ve spektroskopi ile perovskite filmlerin optoelektronik özelliklerini uzamsal çözünürlükle analiz eder.

#### Donanım Bileşenleri

| Bileşen | Spesifikasyon | Amaç |
|---------|---------------|------|
| **Excitation Source** | 405 nm LED, 100 mW | PL uyarımı |
| **Emission Filter** | 700-850 nm bandpass | PL emisyonu izolasyonu |
| **Spectrometer** | Ocean Optics USB4000, 200-1100 nm | Spektral analiz |
| **Imaging Camera** | FLIR Blackfly S, 2.3 MP, monochrome | PL görüntüleme |
| **Fiber Optic** | 400 µm core, UV-VIS | Nokta spektroskopi |
| **XY Stage** | Motorize, 1 µm hassasiyet | Uzamsal tarama |

#### Yazılım Pipeline

```python
class PerImP:
    def __init__(self):
        self.led = ExcitationLED(wavelength=405)
        self.camera = ImagingCamera()
        self.spectrometer = Spectrometer()
        self.stage = MotorizedStage()
        
    def capture_pl_image(self, sample_id, exposure_ms=100):
        """
        PL görüntüsü yakala
        """
        # LED'i aç
        self.led.set_power(50)  # mW
        time.sleep(0.5)  # Stabilizasyon
        
        # Kamera ayarları
        self.camera.set_exposure(exposure_ms)
        self.camera.set_gain(10)
        
        # Görüntü yakala
        pl_image = self.camera.capture()
        
        # LED'i kapat
        self.led.set_power(0)
        
        return pl_image
    
    def measure_pl_spectrum(self, sample_id, position=(0, 0)):
        """
        Belirli bir noktada PL spektrumu ölç
        """
        # Pozisyona git
        self.stage.move_to(*position)
        time.sleep(0.1)
        
        # LED'i aç
        self.led.set_power(50)
        time.sleep(0.5)
        
        # Spektrum ölç
        wavelengths, intensities = self.spectrometer.measure(
            integration_time=100,  # ms
            scans_to_average=5
        )
        
        # LED'i kapat
        self.led.set_power(0)
        
        return wavelengths, intensities
    
    def analyze_pl_image(self, pl_image):
        """
        PL görüntüsünden özellikler çıkar
        """
        features = {}
        
        # 1. Ortalama PL yoğunluğu
        features['mean_pl_intensity'] = np.mean(pl_image)
        
        # 2. PL homojenliği (CV - coefficient of variation)
        features['pl_homogeneity'] = 1 - (np.std(pl_image) / np.mean(pl_image))
        
        # 3. Parlak ve karanlık bölgeler
        threshold = np.percentile(pl_image, 50)
        bright_regions = pl_image > threshold
        features['bright_fraction'] = np.sum(bright_regions) / pl_image.size
        
        # 4. Uzamsal korelasyon
        autocorr = signal.correlate2d(pl_image, pl_image, mode='same')
        features['spatial_correlation'] = autocorr[pl_image.shape[0]//2, pl_image.shape[1]//2]
        
        return features
    
    def analyze_pl_spectrum(self, wavelengths, intensities):
        """
        PL spektrumundan özellikler çıkar
        """
        features = {}
        
        # 1. PL pik pozisyonu
        peak_idx = np.argmax(intensities)
        features['pl_peak_wavelength'] = wavelengths[peak_idx]
        features['pl_peak_intensity'] = intensities[peak_idx]
        
        # 2. Bant aralığı (bandgap) tahmini
        # E_g (eV) = 1240 / λ_peak (nm)
        features['bandgap_estimate'] = 1240 / features['pl_peak_wavelength']
        
        # 3. FWHM (Full Width at Half Maximum)
        half_max = features['pl_peak_intensity'] / 2
        indices = np.where(intensities >= half_max)[0]
        if len(indices) > 1:
            fwhm_wavelength = wavelengths[indices[-1]] - wavelengths[indices[0]]
            features['pl_fwhm'] = fwhm_wavelength
        else:
            features['pl_fwhm'] = 0
        
        # 4. Spektral saflık (peak sharpness)
        features['spectral_purity'] = features['pl_peak_intensity'] / features['pl_fwhm'] if features['pl_fwhm'] > 0 else 0
        
        # 5. Kuyruk emisyonu (tail emission)
        tail_region = wavelengths > (features['pl_peak_wavelength'] + 50)
        if np.any(tail_region):
            features['tail_emission_ratio'] = np.mean(intensities[tail_region]) / features['pl_peak_intensity']
        else:
            features['tail_emission_ratio'] = 0
        
        return features
    
    def multi_point_scan(self, sample_id, grid_size=(5, 5)):
        """
        Çoklu nokta PL spektroskopi taraması
        """
        results = []
        
        x_positions = np.linspace(0, 20, grid_size[0])  # 20 mm tarama alanı
        y_positions = np.linspace(0, 20, grid_size[1])
        
        for x in x_positions:
            for y in y_positions:
                wavelengths, intensities = self.measure_pl_spectrum(sample_id, (x, y))
                features = self.analyze_pl_spectrum(wavelengths, intensities)
                features['position'] = (x, y)
                results.append(features)
        
        return results
    
    def analyze(self, sample_id):
        """
        Tam PerImP analizi
        """
        # 1. PL görüntüsü yakala
        pl_image = self.capture_pl_image(sample_id)
        
        # 2. Görüntü analizi
        image_features = self.analyze_pl_image(pl_image)
        
        # 3. Merkez nokta spektrumu
        wavelengths, intensities = self.measure_pl_spectrum(sample_id, (10, 10))
        spectrum_features = self.analyze_pl_spectrum(wavelengths, intensities)
        
        # 4. Çoklu nokta tarama (opsiyonel, zaman varsa)
        # multi_point_data = self.multi_point_scan(sample_id, grid_size=(3, 3))
        
        # 5. Sonuçları birleştir
        results = {
            'sample_id': sample_id,
            'timestamp': datetime.now().isoformat(),
            'image_features': image_features,
            'spectrum_features': spectrum_features,
            'pl_image_path': f'data/pl_images/{sample_id}_pl.png',
            'spectrum_path': f'data/spectra/{sample_id}_spectrum.csv'
        }
        
        # Verileri kaydet
        cv2.imwrite(results['pl_image_path'], pl_image)
        np.savetxt(results['spectrum_path'], 
                   np.column_stack([wavelengths, intensities]),
                   delimiter=',', header='wavelength,intensity')
        
        return results
```

### 2.2. Performans Metrikleri

- **İşlem Süresi**: 40-50 saniye/örnek
  - PL görüntü yakalama: 15 s
  - Spektrum ölçümü: 10 s
  - Analiz: 10 s
  - Kaydetme: 5 s

- **Spektral Çözünürlük**: 1.5 nm (FWHM)
- **Uzamsal Çözünürlük**: 50 µm (fiber optic spot size)
- **Dinamik Aralık**: 10^4

### 2.3. Çıktı Formatı

```json
{
  "sample_id": "EXP_158",
  "timestamp": "2025-10-04T19:32:15",
  "image_features": {
    "mean_pl_intensity": 45678.5,
    "pl_homogeneity": 0.823,
    "bright_fraction": 0.567,
    "spatial_correlation": 0.912
  },
  "spectrum_features": {
    "pl_peak_wavelength": 785.3,
    "pl_peak_intensity": 89456.2,
    "bandgap_estimate": 1.579,
    "pl_fwhm": 38.5,
    "spectral_purity": 2323.5,
    "tail_emission_ratio": 0.045
  },
  "pl_image_path": "data/pl_images/EXP_158_pl.png",
  "spectrum_path": "data/spectra/EXP_158_spectrum.csv"
}
```

---

## 3. Conductivity Module: 4-Nokta Prob Elektriksel Karakterizasyon

### 3.1. Sistem Mimarisi

Conductivity modülü, perovskite filmlerin elektriksel iletkenliğini ve yüzey direncini tahribatsız olarak ölçer.

#### Donanım Bileşenleri

| Bileşen | Spesifikasyon | Amaç |
|---------|---------------|------|
| **4-Point Probe Head** | Jandel RM3000, 1 mm prob aralığı | Yüzey direnci ölçümü |
| **Source Meter** | Keithley 2400, ±1 µA - 1 A | Akım kaynağı ve voltaj ölçümü |
| **Z-axis Stage** | Motorize, 10 µm hassasiyet | Prob konumlandırma |
| **Force Sensor** | Loadcell, 0-100 g | Prob temas kuvveti kontrolü |
| **Temperature Sensor** | PT100, ±0.1°C | Sıcaklık kompanzasyonu |

#### Yazılım Pipeline

```python
class ConductivityModule:
    def __init__(self):
        self.probe = FourPointProbe()
        self.source_meter = SourceMeter()
        self.z_stage = ZAxisStage()
        self.force_sensor = ForceSensor()
        self.temp_sensor = TemperatureSensor()
        
    def approach_sample(self, target_force=20):
        """
        Probu örneğe kontrollü yaklaştır
        """
        self.z_stage.set_speed(0.5)  # mm/s
        
        while True:
            current_force = self.force_sensor.read()
            
            if current_force >= target_force:
                break
            
            self.z_stage.move_relative(-0.01)  # 10 µm adım
            time.sleep(0.05)
        
        # Stabilizasyon
        time.sleep(0.5)
    
    def measure_resistance(self, current_range=(1e-6, 1e-3)):
        """
        4-nokta direnci ölç
        """
        measurements = []
        
        # Çoklu akım seviyelerinde ölç
        currents = np.logspace(np.log10(current_range[0]), 
                               np.log10(current_range[1]), 
                               num=10)
        
        for I in currents:
            # Pozitif akım
            self.source_meter.set_current(I)
            time.sleep(0.1)  # Stabilizasyon
            V_pos = self.source_meter.measure_voltage()
            
            # Negatif akım (offset eliminasyonu)
            self.source_meter.set_current(-I)
            time.sleep(0.1)
            V_neg = self.source_meter.measure_voltage()
            
            # Ortalama voltaj
            V_avg = (V_pos - V_neg) / 2
            
            # Direnç hesapla
            R = V_avg / I
            
            measurements.append({
                'current': I,
                'voltage': V_avg,
                'resistance': R
            })
        
        # Akımı kapat
        self.source_meter.set_current(0)
        
        return measurements
    
    def calculate_sheet_resistance(self, measurements, film_thickness=None):
        """
        Yüzey direnci hesapla
        """
        # Ortalama direnç
        resistances = [m['resistance'] for m in measurements]
        R_avg = np.mean(resistances)
        R_std = np.std(resistances)
        
        # Yüzey direnci (Ω/sq)
        # R_sheet = (π / ln(2)) * R_measured ≈ 4.532 * R_measured
        correction_factor = np.pi / np.log(2)
        R_sheet = correction_factor * R_avg
        
        results = {
            'resistance_avg': R_avg,
            'resistance_std': R_std,
            'sheet_resistance': R_sheet,
            'sheet_resistance_unit': 'Ohm/sq'
        }
        
        # Eğer film kalınlığı biliniyorsa, hacim iletkenliği hesapla
        if film_thickness is not None:
            # σ = 1 / (R_sheet * t)
            conductivity = 1 / (R_sheet * film_thickness * 1e-7)  # S/cm (t in nm)
            results['conductivity'] = conductivity
            results['conductivity_unit'] = 'S/cm'
            results['film_thickness'] = film_thickness
            results['film_thickness_unit'] = 'nm'
        
        return results
    
    def retract_probe(self):
        """
        Probu örnek yüzeyinden kaldır
        """
        self.z_stage.move_relative(5)  # 5 mm yukarı
        time.sleep(0.5)
    
    def analyze(self, sample_id, film_thickness=None):
        """
        Tam iletkenlik analizi
        """
        # Sıcaklık ölç
        temperature = self.temp_sensor.read()
        
        # 1. Probu örneğe yaklaştır
        self.approach_sample(target_force=20)  # gram
        
        # 2. Direnci ölç
        measurements = self.measure_resistance()
        
        # 3. Yüzey direnci hesapla
        electrical_properties = self.calculate_sheet_resistance(measurements, film_thickness)
        
        # 4. Probu kaldır
        self.retract_probe()
        
        # 5. Sonuçları kaydet
        results = {
            'sample_id': sample_id,
            'timestamp': datetime.now().isoformat(),
            'temperature': temperature,
            'measurements': measurements,
            'electrical_properties': electrical_properties,
            'data_path': f'data/conductivity/{sample_id}_iv.csv'
        }
        
        # I-V verisini kaydet
        iv_data = np.array([[m['current'], m['voltage'], m['resistance']] 
                            for m in measurements])
        np.savetxt(results['data_path'], iv_data, 
                   delimiter=',', 
                   header='current,voltage,resistance')
        
        return results
```

### 3.2. Performans Metrikleri

- **İşlem Süresi**: 30-40 saniye/örnek
  - Prob yaklaşma: 10 s
  - Ölçüm: 15 s
  - Geri çekilme: 5 s

- **Direnç Aralığı**: 1 Ω - 10 MΩ
- **Doğruluk**: ±2% (R < 1 kΩ), ±5% (R > 1 kΩ)
- **Tekrarlanabilirlik**: CV < 3%

### 3.3. Çıktı Formatı

```json
{
  "sample_id": "EXP_158",
  "timestamp": "2025-10-04T19:33:30",
  "temperature": 23.5,
  "measurements": [
    {"current": 1e-6, "voltage": 0.00234, "resistance": 2340},
    {"current": 1e-5, "voltage": 0.02341, "resistance": 2341},
    ...
  ],
  "electrical_properties": {
    "resistance_avg": 2340.5,
    "resistance_std": 12.3,
    "sheet_resistance": 10605.8,
    "sheet_resistance_unit": "Ohm/sq",
    "conductivity": 1.23e-4,
    "conductivity_unit": "S/cm",
    "film_thickness": 450,
    "film_thickness_unit": "nm"
  },
  "data_path": "data/conductivity/EXP_158_iv.csv"
}
```

---

## 4. Tier 1 Entegrasyon ve Veri Akışı

### 4.1. Sıralı Çalıştırma Protokolü

```python
class Tier1Characterization:
    def __init__(self):
        self.perovvision = PerovVision()
        self.perimp = PerImP()
        self.conductivity = ConductivityModule()
        self.database = Database()
        
    def run_full_characterization(self, sample_id, film_thickness=None):
        """
        Tier 1 tam karakterizasyon
        """
        print(f"[Tier 1] Starting characterization for {sample_id}")
        start_time = time.time()
        
        results = {
            'sample_id': sample_id,
            'tier': 1,
            'start_time': datetime.now().isoformat()
        }
        
        # 1. PerovVision (45-60 s)
        print("[1/3] Running PerovVision...")
        try:
            perovvision_results = self.perovvision.analyze(sample_id)
            results['perovvision'] = perovvision_results
            print(f"  ✓ Quality Score: {perovvision_results['quality_score']:.3f}")
        except Exception as e:
            print(f"  ✗ PerovVision failed: {e}")
            results['perovvision'] = None
        
        # 2. PerImP (40-50 s)
        print("[2/3] Running PerImP...")
        try:
            perimp_results = self.perimp.analyze(sample_id)
            results['perimp'] = perimp_results
            print(f"  ✓ Bandgap: {perimp_results['spectrum_features']['bandgap_estimate']:.3f} eV")
        except Exception as e:
            print(f"  ✗ PerImP failed: {e}")
            results['perimp'] = None
        
        # 3. Conductivity (30-40 s)
        print("[3/3] Running Conductivity...")
        try:
            conductivity_results = self.conductivity.analyze(sample_id, film_thickness)
            results['conductivity'] = conductivity_results
            if 'conductivity' in conductivity_results['electrical_properties']:
                print(f"  ✓ Conductivity: {conductivity_results['electrical_properties']['conductivity']:.2e} S/cm")
        except Exception as e:
            print(f"  ✗ Conductivity failed: {e}")
            results['conductivity'] = None
        
        # Toplam süre
        total_time = time.time() - start_time
        results['end_time'] = datetime.now().isoformat()
        results['total_time_seconds'] = total_time
        
        print(f"[Tier 1] Completed in {total_time:.1f} seconds")
        
        # Veritabanına kaydet
        self.database.save_tier1_results(results)
        
        return results
```

### 4.2. Tier 1 Özet Metrikleri

Her Tier 1 karakterizasyonundan sonra, aşağıdaki özet metrikleri hesaplanır ve ML optimizasyon motoruna gönderilir:

```python
def calculate_tier1_summary(tier1_results):
    """
    Tier 1 sonuçlarından özet metrikleri hesapla
    """
    summary = {}
    
    # PerovVision'dan
    if tier1_results['perovvision']:
        pv = tier1_results['perovvision']['features']
        summary['coverage'] = pv['coverage']
        summary['defect_density'] = pv['defect_density']
        summary['homogeneity'] = pv['homogeneity']
        summary['quality_score'] = tier1_results['perovvision']['quality_score']
    
    # PerImP'den
    if tier1_results['perimp']:
        pi = tier1_results['perimp']['spectrum_features']
        summary['bandgap'] = pi['bandgap_estimate']
        summary['pl_intensity'] = pi['pl_peak_intensity']
        summary['pl_fwhm'] = pi['pl_fwhm']
        summary['spectral_purity'] = pi['spectral_purity']
    
    # Conductivity'den
    if tier1_results['conductivity']:
        cond = tier1_results['conductivity']['electrical_properties']
        summary['sheet_resistance'] = cond['sheet_resistance']
        if 'conductivity' in cond:
            summary['conductivity'] = cond['conductivity']
    
    # Genel başarı skoru (0-1)
    summary['tier1_success_score'] = calculate_success_score(summary)
    
    return summary

def calculate_success_score(summary):
    """
    Tier 1 başarı skoru hesapla
    """
    score = 0.0
    count = 0
    
    # Kalite (PerovVision)
    if 'quality_score' in summary:
        score += summary['quality_score']
        count += 1
    
    # Optik (PerImP)
    if 'spectral_purity' in summary:
        # Normalize edilmiş spektral saflık (0-1)
        norm_purity = np.clip(summary['spectral_purity'] / 5000, 0, 1)
        score += norm_purity
        count += 1
    
    # Elektriksel (Conductivity)
    if 'conductivity' in summary:
        # İyi iletkenlik: 1e-5 - 1e-3 S/cm arası
        log_cond = np.log10(summary['conductivity'])
        norm_cond = np.clip((log_cond + 5) / 2, 0, 1)  # -5 to -3 → 0 to 1
        score += norm_cond
        count += 1
    
    return score / count if count > 0 else 0.0
```

---

## 5. Tier 2 ve Tier 3 Önizleme

### Tier 2: Detaylı Karakterizasyon (Seçilmiş Örnekler)

Tier 1'de başarı skoru > 0.7 olan örnekler için:

- **XRD (X-Ray Diffraction)**: Faz saflığı, kristal yapı, tercihli yönelim
- **UV-Vis Transmittans**: Optik bant aralığı (Tauc plot)
- **Profilometre**: Film kalınlığı ve yüzey pürüzlülüğü

**Süre**: 15-20 dakika/örnek

### Tier 3: Uzun Süreli Testler (En İyi Örnekler)

Tier 2'de en iyi performans gösteren örnekler için:

- **Hızlandırılmış Yaşlandırma**: 85°C, 85% RH, sürekli aydınlatma
- **J-V Karakterizasyonu**: Basit cihaz yapımı ve verimlilik ölçümü
- **EQE (External Quantum Efficiency)**: Spektral yanıt

**Süre**: > 24 saat

---

## 6. Veri Yapısı ve Veritabanı Şeması

```sql
-- Tier 1 Karakterizasyon Tablosu
CREATE TABLE tier1_characterization (
    id SERIAL PRIMARY KEY,
    sample_id VARCHAR(50) NOT NULL,
    experiment_id VARCHAR(50) NOT NULL,
    timestamp TIMESTAMP NOT NULL,
    
    -- PerovVision
    coverage FLOAT,
    defect_density FLOAT,
    homogeneity FLOAT,
    quality_score FLOAT,
    raw_image_path VARCHAR(255),
    mask_path VARCHAR(255),
    viz_path VARCHAR(255),
    
    -- PerImP
    bandgap FLOAT,
    pl_peak_wavelength FLOAT,
    pl_peak_intensity FLOAT,
    pl_fwhm FLOAT,
    spectral_purity FLOAT,
    pl_image_path VARCHAR(255),
    spectrum_path VARCHAR(255),
    
    -- Conductivity
    sheet_resistance FLOAT,
    conductivity FLOAT,
    temperature FLOAT,
    iv_data_path VARCHAR(255),
    
    -- Summary
    tier1_success_score FLOAT,
    total_time_seconds FLOAT,
    
    FOREIGN KEY (experiment_id) REFERENCES experiments(id)
);

-- İndeksler
CREATE INDEX idx_sample_id ON tier1_characterization(sample_id);
CREATE INDEX idx_experiment_id ON tier1_characterization(experiment_id);
CREATE INDEX idx_timestamp ON tier1_characterization(timestamp);
CREATE INDEX idx_success_score ON tier1_characterization(tier1_success_score);
```

Bu Tier 1 karakterizasyon sistemi, hızlı, güvenilir ve makine öğrenmesi destekli optimizasyon için zengin veri sağlar.
