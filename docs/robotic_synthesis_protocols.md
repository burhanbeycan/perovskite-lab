# Robotik Sentez Protokolleri - Detaylı Teknik Dokümantasyon

## Genel Bakış

Robotik sentez sistemi (ROSIE - Robotic Solution Integration & Experiment), perovskite ince film üretimini tam otomatik olarak gerçekleştirir. Sistem, çözelti hazırlama, substrat işleme, film kaplama ve tavlama adımlarını insan müdahalesi olmadan yürütür.

---

## 1. Sistem Mimarisi

### 1.1. Donanım Bileşenleri

| Bileşen | Spesifikasyon | Fonksiyon |
|---------|---------------|-----------|
| **Robotik Kol** | 6-DOF, ±0.05 mm tekrarlanabilirlik | Ana manipülasyon |
| **Pipetleme Ünitesi** | 8-kanal, 1-1000 µL | Sıvı taşıma |
| **Stok Çözelti Rafı** | 8 pozisyon, sıcaklık kontrollü (15°C) | Prekursor depolama |
| **Karıştırma İstasyonu** | 24 pozisyon, manyetik karıştırıcı | Çözelti hazırlama |
| **Spin Coater** | 0-6000 rpm, ±10 rpm hassasiyet | Film kaplama |
| **Anti-solvent Dispenseri** | Hassas dozajlama, 50-500 µL | Kristalizasyon kontrolü |
| **Tavlama Plakası** | 6 pozisyon, 50-200°C, ±0.5°C | Termal işlem |
| **Substrat Magazini** | 50 pozisyon, N₂ ortamı | Substrat depolama |
| **Glovebox** | O₂ < 1 ppm, H₂O < 1 ppm | İnert atmosfer |

### 1.2. Yazılım Mimarisi

```python
# Robotik Sentez Sistemi - Ana Kontrol Yazılımı

class ROSIEController:
    def __init__(self):
        self.robot = RobotArm(port='/dev/ttyUSB0')
        self.pipette = MultiChannelPipette(channels=8)
        self.spin_coater = SpinCoater(port='/dev/ttyUSB1')
        self.hotplate = Hotplate(port='/dev/ttyUSB2')
        self.dispenser = AntiSolventDispenser()
        self.stock_rack = StockSolutionRack(temperature=15)
        self.mixing_station = MixingStation(positions=24)
        self.substrate_magazine = SubstrateMagazine(capacity=50)
        self.database = Database()
        self.logger = Logger()
        
    def initialize_system(self):
        """
        Sistem başlatma ve kalibrasyon
        """
        self.logger.info("Initializing ROSIE system...")
        
        # 1. Robot kalibrasyonu
        self.robot.home()
        self.robot.calibrate()
        
        # 2. Pipet kalibrasyonu
        self.pipette.calibrate()
        
        # 3. Spin coater testi
        self.spin_coater.test_run()
        
        # 4. Hotplate sıcaklık kontrolü
        self.hotplate.set_temperature(25)
        self.hotplate.wait_stable()
        
        # 5. Stok çözelti kontrolü
        stock_levels = self.stock_rack.check_levels()
        for i, level in enumerate(stock_levels):
            if level < 5:  # mL
                self.logger.warning(f"Stock solution {i+1} low: {level} mL")
        
        # 6. Substrat sayımı
        substrate_count = self.substrate_magazine.count()
        self.logger.info(f"Substrates available: {substrate_count}")
        
        self.logger.info("ROSIE system ready!")
```

---

## 2. Çözelti Hazırlama Protokolü

### 2.1. Stok Çözeltileri

| ID | Bileşen | Konsantrasyon | Solvent | Saklama |
|----|---------|---------------|---------|---------|
| S1 | PbI₂ | 1.5 M | DMF:DMSO (4:1) | 15°C, karanlık |
| S2 | PbBr₂ | 1.5 M | DMF:DMSO (4:1) | 15°C, karanlık |
| S3 | FAI (Formamidinium Iodide) | 1.5 M | DMF:DMSO (4:1) | 15°C, karanlık |
| S4 | MABr (Methylammonium Bromide) | 1.5 M | DMF:DMSO (4:1) | 15°C, karanlık |
| S5 | CsI | 1.5 M | DMSO | 15°C, karanlık |
| S6 | Additives (DMSO, Thiourea, vb.) | Varies | - | RT |
| S7 | Anti-solvent (Chlorobenzene) | - | - | RT |
| S8 | Cleaning solvent (IPA) | - | - | RT |

### 2.2. Çözelti Hazırlama Algoritması

```python
def prepare_solution(self, composition, total_volume=1000):
    """
    Perovskite çözeltisi hazırla
    
    Args:
        composition: dict, örn. {'FA': 0.8, 'Cs': 0.2, 'I': 0.9, 'Br': 0.1}
        total_volume: int, toplam hacim (µL)
    
    Returns:
        vial_position: int, hazırlanan çözeltinin pozisyonu
    """
    self.logger.info(f"Preparing solution: {composition}")
    
    # 1. Boş vial seç
    vial_position = self.mixing_station.get_empty_vial()
    
    # 2. Hacim hesaplamaları
    # Perovskite formülü: A_x B_y Pb X_3
    # A-site: FA, MA, Cs
    # B-site: Pb (sabit)
    # X-site: I, Br, Cl
    
    # A-site oranları
    fa_ratio = composition.get('FA', 0)
    ma_ratio = composition.get('MA', 0)
    cs_ratio = composition.get('Cs', 0)
    
    # X-site oranları
    i_ratio = composition.get('I', 0)
    br_ratio = composition.get('Br', 0)
    cl_ratio = composition.get('Cl', 0)
    
    # Toplam konsantrasyon (örn. 1.2 M)
    target_concentration = composition.get('concentration', 1.2)
    
    # Hacim hesaplamaları (basitleştirilmiş)
    # Gerçek hesaplamalar stokiyometriyi dikkate almalı
    
    volumes = {}
    
    # PbI₂ ve PbBr₂ (X-site oranına göre)
    volumes['S1_PbI2'] = total_volume * i_ratio * 0.4  # µL
    volumes['S2_PbBr2'] = total_volume * br_ratio * 0.4
    
    # A-site katyonları
    volumes['S3_FAI'] = total_volume * fa_ratio * 0.3
    volumes['S4_MABr'] = total_volume * ma_ratio * 0.3
    volumes['S5_CsI'] = total_volume * cs_ratio * 0.3
    
    # Katkı maddeleri (opsiyonel)
    if 'additive' in composition:
        volumes['S6_Additive'] = composition['additive_volume']
    
    # 3. Pipetleme sırası
    pipetting_order = ['S1_PbI2', 'S2_PbBr2', 'S3_FAI', 'S4_MABr', 'S5_CsI']
    
    for stock_id in pipetting_order:
        if volumes.get(stock_id, 0) > 0:
            volume = volumes[stock_id]
            stock_number = int(stock_id[1])  # S1 -> 1
            
            self.logger.info(f"  Pipetting {volume:.1f} µL from {stock_id}")
            
            # Robot stok çözeltiye git
            self.robot.move_to_stock(stock_number)
            
            # Pipet aspirate
            self.pipette.aspirate(volume, speed='slow')
            
            # Robot karıştırma istasyonuna git
            self.robot.move_to_mixing_station(vial_position)
            
            # Pipet dispense
            self.pipette.dispense(volume, speed='slow', mix_after=True)
            
            # Pipet temizle
            self.pipette.wash()
    
    # 4. Karıştırma
    self.logger.info("  Mixing solution...")
    self.mixing_station.start_stirring(vial_position, rpm=500)
    time.sleep(300)  # 5 dakika karıştırma
    self.mixing_station.stop_stirring(vial_position)
    
    # 5. Çözelti bilgilerini kaydet
    solution_info = {
        'vial_position': vial_position,
        'composition': composition,
        'volumes': volumes,
        'total_volume': total_volume,
        'preparation_time': datetime.now().isoformat()
    }
    
    self.database.save_solution_info(solution_info)
    
    self.logger.info(f"Solution prepared at position {vial_position}")
    
    return vial_position
```

---

## 3. Substrat Hazırlama Protokolü

### 3.1. Substrat Spesifikasyonları

- **Malzeme**: ITO-kaplı cam (15 Ω/sq)
- **Boyut**: 25 mm × 25 mm × 1.1 mm
- **Temizlik**: Ultrasonik temizlik (deterjanlı su, DI su, IPA)
- **UV-Ozon**: 15 dakika (yüzey aktivasyonu)
- **Depolama**: N₂ ortamında, substrat magazininde

### 3.2. Substrat Yükleme ve Hazırlama

```python
def prepare_substrate(self, substrate_id):
    """
    Substratı spin coater'a yükle ve hazırla
    """
    self.logger.info(f"Preparing substrate {substrate_id}")
    
    # 1. Substratı magazinden al
    self.robot.move_to_magazine()
    substrate = self.substrate_magazine.get_substrate(substrate_id)
    self.robot.grip_substrate(substrate)
    
    # 2. UV-Ozon temizliği (opsiyonel, zaten temizse atla)
    if substrate.needs_cleaning:
        self.robot.move_to_uv_ozone()
        self.uv_ozone.treat(duration=900)  # 15 dakika
    
    # 3. Spin coater'a yerleştir
    self.robot.move_to_spin_coater()
    self.robot.place_substrate()
    self.robot.release()
    
    # 4. Vakum tut
    self.spin_coater.vacuum_on()
    time.sleep(1)
    
    self.logger.info(f"Substrate {substrate_id} ready for coating")
    
    return True
```

---

## 4. Film Kaplama Protokolü (Spin Coating)

### 4.1. Spin Coating Parametreleri

| Parametre | Aralık | Tipik Değer | Etki |
|-----------|--------|-------------|------|
| **Çözelti Hacmi** | 50-150 µL | 100 µL | Film kalınlığı |
| **Spread Speed** | 500-1000 rpm | 1000 rpm | Homojen dağılım |
| **Spread Time** | 5-10 s | 10 s | - |
| **Spin Speed** | 2000-6000 rpm | 4000 rpm | Film kalınlığı |
| **Spin Time** | 20-60 s | 30 s | - |
| **Acceleration** | 500-2000 rpm/s | 1000 rpm/s | Film homojenliği |
| **Anti-solvent Timing** | 5-25 s | 15 s | Kristalizasyon |
| **Anti-solvent Volume** | 100-300 µL | 200 µL | Kristal boyutu |

### 4.2. Spin Coating Algoritması

```python
def spin_coat_film(self, solution_vial, substrate_id, parameters):
    """
    Spin coating ile film kapla
    
    Args:
        solution_vial: int, çözelti pozisyonu
        substrate_id: str, substrat ID
        parameters: dict, spin coating parametreleri
    """
    self.logger.info(f"Spin coating {substrate_id} with solution from vial {solution_vial}")
    
    # Parametreleri al
    solution_volume = parameters.get('solution_volume', 100)  # µL
    spread_speed = parameters.get('spread_speed', 1000)  # rpm
    spread_time = parameters.get('spread_time', 10)  # s
    spin_speed = parameters.get('spin_speed', 4000)  # rpm
    spin_time = parameters.get('spin_time', 30)  # s
    acceleration = parameters.get('acceleration', 1000)  # rpm/s
    antisolvent_timing = parameters.get('antisolvent_timing', 15)  # s
    antisolvent_volume = parameters.get('antisolvent_volume', 200)  # µL
    
    # 1. Çözelti yükle
    self.robot.move_to_mixing_station(solution_vial)
    self.pipette.aspirate(solution_volume, speed='slow')
    
    # 2. Substrata git
    self.robot.move_to_spin_coater()
    
    # 3. Çözeltiyi substrat üzerine damla
    self.pipette.dispense(solution_volume, height=5, speed='medium')  # 5 mm yükseklik
    time.sleep(2)  # Çözeltinin yayılması için bekle
    
    # 4. Spread aşaması
    self.logger.info(f"  Spreading at {spread_speed} rpm for {spread_time} s")
    self.spin_coater.spin(speed=spread_speed, time=spread_time, acceleration=acceleration)
    
    # 5. Ana spin aşaması (anti-solvent ile)
    self.logger.info(f"  Spinning at {spin_speed} rpm for {spin_time} s")
    
    # Anti-solvent dispenserini hazırla
    self.dispenser.load_antisolvent(antisolvent_volume)
    
    # Spin başlat
    self.spin_coater.start_spin(speed=spin_speed, acceleration=acceleration)
    
    # Anti-solvent zamanlaması
    time.sleep(antisolvent_timing)
    
    self.logger.info(f"  Dispensing {antisolvent_volume} µL anti-solvent")
    self.dispenser.dispense(antisolvent_volume, dispense_time=1.0)  # 1 saniyede damla
    
    # Kalan spin süresi
    remaining_time = spin_time - antisolvent_timing
    time.sleep(remaining_time)
    
    # Spin durdur
    self.spin_coater.stop()
    
    # 6. Film görsel kontrolü (opsiyonel)
    if parameters.get('visual_check', False):
        image = self.camera.capture()
        film_quality = self.quick_visual_check(image)
        self.logger.info(f"  Visual check: {film_quality}")
    
    self.logger.info(f"Spin coating completed for {substrate_id}")
    
    return True

def quick_visual_check(self, image):
    """
    Hızlı görsel kontrol (film oluştu mu?)
    """
    # Basit renk analizi
    mean_color = np.mean(image, axis=(0, 1))
    
    # Perovskite filmleri tipik olarak koyu kahverengi/siyah
    # RGB değerleri düşük olmalı
    if np.mean(mean_color) < 100:
        return "GOOD"
    else:
        return "POOR"
```

---

## 5. Tavlama Protokolü

### 5.1. Tavlama Parametreleri

| Parametre | Aralık | Tipik Değer | Etki |
|-----------|--------|-------------|------|
| **Sıcaklık** | 80-180°C | 120°C | Kristalizasyon |
| **Süre** | 5-120 dakika | 30 dakika | Kristal büyümesi |
| **Rampa Hızı** | 1-10°C/min | 5°C/min | Stres kontrolü |
| **Soğutma Hızı** | 0.5-5°C/min | 2°C/min | Kusur minimizasyonu |
| **Atmosfer** | N₂, Ar, hava | N₂ | Oksidasyon önleme |

### 5.2. Tavlama Algoritması

```python
def anneal_film(self, substrate_id, parameters):
    """
    Filmi tavla
    
    Args:
        substrate_id: str, substrat ID
        parameters: dict, tavlama parametreleri
    """
    self.logger.info(f"Annealing {substrate_id}")
    
    # Parametreleri al
    target_temp = parameters.get('annealing_temp', 120)  # °C
    anneal_time = parameters.get('annealing_time', 30)  # dakika
    ramp_rate = parameters.get('ramp_rate', 5)  # °C/min
    cool_rate = parameters.get('cool_rate', 2)  # °C/min
    
    # 1. Substratı spin coater'dan al
    self.spin_coater.vacuum_off()
    time.sleep(1)
    self.robot.move_to_spin_coater()
    self.robot.grip_substrate()
    
    # 2. Hotplate'e taşı
    hotplate_position = self.hotplate.get_empty_position()
    self.robot.move_to_hotplate(hotplate_position)
    self.robot.place_substrate()
    self.robot.release()
    
    # 3. Sıcaklık rampa
    current_temp = self.hotplate.get_temperature(hotplate_position)
    ramp_time = (target_temp - current_temp) / ramp_rate  # dakika
    
    self.logger.info(f"  Ramping to {target_temp}°C at {ramp_rate}°C/min")
    self.hotplate.set_temperature(hotplate_position, target_temp, ramp_rate=ramp_rate)
    time.sleep(ramp_time * 60)  # saniyeye çevir
    
    # 4. Tavlama
    self.logger.info(f"  Annealing at {target_temp}°C for {anneal_time} min")
    time.sleep(anneal_time * 60)
    
    # 5. Soğutma
    self.logger.info(f"  Cooling at {cool_rate}°C/min")
    cool_time = (target_temp - 25) / cool_rate  # dakika
    self.hotplate.set_temperature(hotplate_position, 25, ramp_rate=-cool_rate)
    time.sleep(cool_time * 60)
    
    # 6. Substratı al ve karakterizasyona gönder
    self.robot.move_to_hotplate(hotplate_position)
    self.robot.grip_substrate()
    
    # Karakterizasyon istasyonuna taşı
    self.robot.move_to_characterization_station()
    self.robot.place_substrate()
    self.robot.release()
    
    self.logger.info(f"Annealing completed for {substrate_id}")
    
    return True
```

---

## 6. Tam Sentez Protokolü (Entegre)

### 6.1. Ana Sentez Döngüsü

```python
def run_synthesis_experiment(self, experiment_id, composition, process_parameters):
    """
    Tam sentez deneyi çalıştır
    
    Args:
        experiment_id: str, deney ID (örn. "EXP_158")
        composition: dict, perovskite kompozisyonu
        process_parameters: dict, proses parametreleri
    
    Returns:
        dict, sentez sonuçları
    """
    self.logger.info(f"Starting synthesis experiment {experiment_id}")
    start_time = time.time()
    
    results = {
        'experiment_id': experiment_id,
        'composition': composition,
        'process_parameters': process_parameters,
        'start_time': datetime.now().isoformat(),
        'status': 'running'
    }
    
    try:
        # 1. Çözelti hazırla (5-10 dakika)
        self.logger.info("[1/4] Preparing solution...")
        solution_vial = self.prepare_solution(
            composition=composition,
            total_volume=process_parameters.get('solution_volume', 1000)
        )
        results['solution_vial'] = solution_vial
        
        # 2. Substrat hazırla (2-3 dakika)
        self.logger.info("[2/4] Preparing substrate...")
        substrate_id = f"{experiment_id}_substrate"
        self.prepare_substrate(substrate_id)
        results['substrate_id'] = substrate_id
        
        # 3. Film kapla (2-3 dakika)
        self.logger.info("[3/4] Spin coating film...")
        self.spin_coat_film(
            solution_vial=solution_vial,
            substrate_id=substrate_id,
            parameters=process_parameters['spin_coating']
        )
        
        # 4. Tavla (30-120 dakika)
        self.logger.info("[4/4] Annealing film...")
        self.anneal_film(
            substrate_id=substrate_id,
            parameters=process_parameters['annealing']
        )
        
        # Başarılı
        results['status'] = 'completed'
        results['end_time'] = datetime.now().isoformat()
        results['total_time_seconds'] = time.time() - start_time
        
        self.logger.info(f"Synthesis experiment {experiment_id} completed successfully")
        
    except Exception as e:
        # Hata
        results['status'] = 'failed'
        results['error'] = str(e)
        results['end_time'] = datetime.now().isoformat()
        
        self.logger.error(f"Synthesis experiment {experiment_id} failed: {e}")
    
    # Veritabanına kaydet
    self.database.save_synthesis_results(results)
    
    return results
```

### 6.2. Örnek Kullanım

```python
# ROSIE sistemini başlat
rosie = ROSIEController()
rosie.initialize_system()

# Deney parametreleri
experiment_id = "EXP_158"

composition = {
    'FA': 0.85,
    'Cs': 0.15,
    'I': 0.9,
    'Br': 0.1,
    'concentration': 1.2  # M
}

process_parameters = {
    'solution_volume': 1000,  # µL
    'spin_coating': {
        'solution_volume': 100,  # µL
        'spread_speed': 1000,  # rpm
        'spread_time': 10,  # s
        'spin_speed': 4000,  # rpm
        'spin_time': 30,  # s
        'acceleration': 1000,  # rpm/s
        'antisolvent_timing': 15,  # s
        'antisolvent_volume': 200,  # µL
        'visual_check': True
    },
    'annealing': {
        'annealing_temp': 120,  # °C
        'annealing_time': 30,  # dakika
        'ramp_rate': 5,  # °C/min
        'cool_rate': 2  # °C/min
    }
}

# Sentezi çalıştır
results = rosie.run_synthesis_experiment(
    experiment_id=experiment_id,
    composition=composition,
    process_parameters=process_parameters
)

print(f"Synthesis status: {results['status']}")
if results['status'] == 'completed':
    print(f"Total time: {results['total_time_seconds']/60:.1f} minutes")
```

---

## 7. Hata Yönetimi ve Güvenlik

### 7.1. Hata Senaryoları ve Çözümleri

| Hata | Tespit | Çözüm |
|------|--------|-------|
| **Stok çözelti bitti** | Seviye sensörü | Operatöre bildirim, deneyi duraklat |
| **Pipet tıkanması** | Basınç sensörü | Pipeti temizle, tekrar dene |
| **Substrat düşmesi** | Vakum sensörü | Deneyi iptal et, substratı değiştir |
| **Spin coater arızası** | Hız sensörü | Acil durdur, bakım gerekli |
| **Sıcaklık kontrolü** | Termoçift | Hotplate'i kapat, soğumayı bekle |
| **Robot konum hatası** | Enkoder | Home pozisyonuna dön, kalibrasyon |

### 7.2. Güvenlik Protokolleri

```python
class SafetyMonitor:
    def __init__(self, rosie_controller):
        self.rosie = rosie_controller
        self.emergency_stop = False
        
    def monitor(self):
        """
        Sürekli güvenlik kontrolü
        """
        while not self.emergency_stop:
            # 1. Sıcaklık kontrolü
            for pos in range(6):
                temp = self.rosie.hotplate.get_temperature(pos)
                if temp > 200:  # Maksimum güvenli sıcaklık
                    self.trigger_emergency_stop("Hotplate overheating!")
            
            # 2. Robot pozisyon kontrolü
            if not self.rosie.robot.is_within_workspace():
                self.trigger_emergency_stop("Robot out of bounds!")
            
            # 3. Glovebox atmosfer kontrolü
            o2_level = self.rosie.glovebox.get_o2_level()
            h2o_level = self.rosie.glovebox.get_h2o_level()
            if o2_level > 100 or h2o_level > 10:  # ppm
                self.logger.warning("Glovebox atmosphere compromised!")
            
            time.sleep(1)  # 1 saniyede bir kontrol
    
    def trigger_emergency_stop(self, reason):
        """
        Acil durdurma
        """
        self.logger.critical(f"EMERGENCY STOP: {reason}")
        self.emergency_stop = True
        
        # Tüm hareketleri durdur
        self.rosie.robot.stop()
        self.rosie.spin_coater.stop()
        
        # Hotplate'leri güvenli sıcaklığa getir
        for pos in range(6):
            self.rosie.hotplate.set_temperature(pos, 25, ramp_rate=-10)
        
        # Operatöre bildirim
        self.send_alert(reason)
```

---

## 8. Performans Metrikleri

### 8.1. Sentez Süreleri

| Adım | Süre | Notlar |
|------|------|--------|
| Çözelti hazırlama | 5-10 dakika | Karıştırma dahil |
| Substrat hazırlama | 2-3 dakika | UV-Ozon opsiyonel |
| Spin coating | 2-3 dakika | Anti-solvent dahil |
| Tavlama | 30-120 dakika | Sıcaklığa bağlı |
| **Toplam (min)** | **40-140 dakika** | Tavlama dominant |

### 8.2. Verimlilik

- **Throughput**: 10-20 örnek/gün (paralel tavlama ile)
- **Tekrarlanabilirlik**: CV < 5% (film kalınlığı)
- **Başarı Oranı**: > 95% (film oluşumu)
- **Malzeme Kullanımı**: < 1 mL çözelti/örnek

---

## 9. Veritabanı Şeması (Sentez)

```sql
-- Sentez Deneyleri Tablosu
CREATE TABLE synthesis_experiments (
    id SERIAL PRIMARY KEY,
    experiment_id VARCHAR(50) UNIQUE NOT NULL,
    composition JSONB NOT NULL,
    process_parameters JSONB NOT NULL,
    solution_vial INTEGER,
    substrate_id VARCHAR(50),
    start_time TIMESTAMP NOT NULL,
    end_time TIMESTAMP,
    total_time_seconds FLOAT,
    status VARCHAR(20),  -- running, completed, failed
    error TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Çözelti Bilgileri Tablosu
CREATE TABLE solutions (
    id SERIAL PRIMARY KEY,
    vial_position INTEGER NOT NULL,
    composition JSONB NOT NULL,
    volumes JSONB NOT NULL,
    total_volume FLOAT,
    preparation_time TIMESTAMP NOT NULL,
    used BOOLEAN DEFAULT FALSE,
    experiment_id VARCHAR(50),
    FOREIGN KEY (experiment_id) REFERENCES synthesis_experiments(experiment_id)
);

-- İndeksler
CREATE INDEX idx_experiment_id ON synthesis_experiments(experiment_id);
CREATE INDEX idx_status ON synthesis_experiments(status);
CREATE INDEX idx_start_time ON synthesis_experiments(start_time);
```

Bu robotik sentez protokolü, tam otomatik ve tekrarlanabilir perovskite ince film üretimini sağlar.
