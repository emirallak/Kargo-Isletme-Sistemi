# Kocaeli Kargo İşletme Sistemi

> A Flask web app that plans cargo delivery routes across Kocaeli on the real road network (OSMnx + Dijkstra) and compares fleet scenarios by distance and cost.

Kocaeli Üniversitesi Bilgisayar Mühendisliği, **Yazılım Laboratuvarı** kapsamında geliştirilmiş bir rota planlama uygulaması. Kullanıcılar istasyonlara kargo gönderir. Yönetici bekleyen kargolar için araç atamasını ve rotaları hesaplar, sonuçları farklı senaryolar arasında maliyete göre karşılaştırır.

## Özellikler

- **İki ayrı panel:**
  - **Kullanıcı paneli:** İstasyon seçerek ağırlık ve açıklamayla kargo gönderme.
  - **Yönetici paneli:** İstasyon ekleme ve silme, rota hesaplama, geçmiş rotaları görüntüleme ve silme.
- **Gerçek yol ağı:** OSMnx ile Kocaeli'nin OpenStreetMap yol ağı indirilir. İstasyonlar arası mesafeler NetworkX üzerinde **Dijkstra** ile hesaplanır. Hesaplanan rotalar tekrar kullanılmak üzere önbelleğe alınır.
- **Rota sıralama:** Her aracın ziyaret sırası **Nearest Neighbor** sezgiseli ile belirlenir.
- **Yük dağıtımı ve senaryo karşılaştırması:**
  - **Senaryo A:** Tüm yük sığıyorsa tek araç kullanılır.
  - **Senaryo B:** Çoklu araç, *Best Fit Decreasing* yük dağıtımı. Önce özmal araçlar doldurulur, gerekirse kiralık araç eklenir.
  - **Senaryo C:** Yalnızca özmal araçlar kullanılır. Kapasiteyi aşan yükler raporlanır.
- **Maliyet modeli:** Toplam maliyet = toplam km + kiralanan araç başına 200 birim. En düşük maliyetli senaryo veritabanına kaydedilir ve ilgili kargolar "atandı" durumuna geçer.
- **Rota görüntüleme:** Araç ve kargo bazında rota detayları ve harita üzerinde güzergâh koordinatları.

## Teknolojiler

| Alan | Teknoloji |
| --- | --- |
| Backend | Python, Flask |
| Graf ve harita | OSMnx, NetworkX (Dijkstra) |
| Veritabanı | MySQL (`mysql-connector-python`) |
| Arayüz | HTML, CSS, JavaScript (Jinja2 şablonları) |

## Proje Yapısı

```
kocaeli_cargo_system/
├── backend/
│   ├── app.py          # Flask uygulaması, API uçları, senaryo motoru
│   ├── algorithms.py   # Nearest Neighbor ve yük dağıtım algoritmaları
│   ├── osm_helper.py   # OSMnx yol ağı ve en kısa yol hesapları
│   ├── models.py       # Station ve Vehicle modelleri (kapasite, maliyet)
│   └── db_manager.py   # MySQL erişim katmanı
├── database/schema.sql # Veritabanı şeması
├── setup_tables.py     # Tabloları oluşturma betiği
├── templates/          # Admin ve kullanıcı sayfaları
└── static/             # CSS ve JS
```

## Kurulum ve Çalıştırma

1. Depodaki `yazlab3.zip` arşivini açın.
2. Bağımlılıkları kurun:

   ```bash
   cd yazlab6/kocaeli_cargo_system
   pip install -r requirements.txt
   ```

3. MySQL'i hazırlayın:
   - Veritabanı bağlantı bilgilerini `backend/db_manager.py` ve `setup_tables.py` içinde düzenleyin.
   - Ardından tabloları oluşturun:

   ```bash
   python setup_tables.py
   ```

4. Uygulamayı başlatın:

   ```bash
   python backend/app.py
   ```

5. Tarayıcıda `http://localhost:5000` adresini açın. İlk rota hesaplamasında yol ağı OpenStreetMap'ten indirileceği için işlem biraz uzun sürebilir.

## API Uçları (özet)

| Yöntem | Uç | Açıklama |
| --- | --- | --- |
| GET/POST | `/api/stations` | İstasyonları listele / ekle |
| POST | `/api/solve` | Bekleyen kargolar için senaryoları hesapla |
| GET | `/api/routes` | Kayıtlı rotaları listele |
| GET | `/api/vehicle-details/<id>` | Araç rota ve yük detayı |
| GET | `/api/cargo/<id>/route` | Bir kargonun izlediği rota |

## Geliştirici

**Muhammed Emir Allak**: [GitHub](https://github.com/emirallak) · [LinkedIn](https://www.linkedin.com/in/muhammedemir-allak-a445a3246/)
