# 🚕 Udemy Veri Bilimi ve Makine Öğrenmesi: 100 Günlük Kamp — 6. Ödev

Bu depo, Udemy’de aldığım *Makine Öğrenmesi Kursu* kapsamında verilen **6. ödev** için hazırlanmıştır. Bu ödevde New York City taksi yolculukları veri seti kullanılarak bir **regresyon modeli** geliştirilmiş, ücreti tahmin etmede coğrafi ve zamansal özelliklerin etkisi analiz edilmiştir.

---

## 🎯 Proje Amacı

### **Temel Amaç**
New York City taksi yolculuklarının ücretini (`fare_amount`) başlangıç ve bitiş koordinatları ile yolculuk zamanına dayanarak tahmin eden, yüksek performanslı bir regresyon modeli geliştirmek.

### **Kritik Değerlendirme**
Gürültülü (noisy) ve doğrusal olmayan ilişkilere sahip bu veri setinde, **Gradient Boosting** algoritmalarının (XGBoost ve LightGBM) performansı karşılaştırılmış ve en iyi model için **GridSearchCV** ile hiperparametre optimizasyonu yapılmıştır.

---

## 🌷 Kullanılan Veri Seti

| Kriter | Detay |
| :--- | :--- |
| **Veri Seti** | uber.csv (NYC Taksi Ücretleri) |
| **Hedef Değişken** | fare_amount (Ücret) |
| **Problem Tipi** | Regresyon |

---

## 🛠️ Proje Aşamaları ve Ön İşleme

Proje, veri kalitesini artırmaya odaklanan kapsamlı temizlik ve özellik mühendisliği aşamalarından oluşmuştur.

### 🔹 1. Veri Temizliği (Data Cleaning)

- **Eksik Değer Yönetimi:** Eksik bırakma koordinatları (dropoff\_longitude/latitude) ve mantık dışı yolcu sayıları ($0$ ve $208$) içeren satırlar silinmiştir.
- **Aykırı Değer Temizliği:**
    - Ücretler fare_amount için mantıksal sınırlar ($\$2.50$ - $\$100$) belirlenerek uç değerler atılmıştır.
    - Koordinatlardaki hatalı $0.0$ (Okyanus) ve dünya sınırları dışındaki değerler temizlenmiştir.
- **Ölçekleme:** Tüm bağımsız değişkenler (`X`) **StandardScaler** kullanılarak ölçeklenmiştir.

### 🔹 2. Özellik Mühendisliği (Feature Engineering)

- **Mesafe Hesabı (Kritik):** Alım ve bırakma koordinatları kullanılarak **Haversine Formülü** ile yolculuğun kuş uçuşu mesafesi (`distance_km`) kilometre cinsinden hesaplanmıştır.
    - Tahmin gücü zayıf olan ham koordinat kolonları (`pickup/dropoff longitude/latitude`) hesaplama sonrası veri setinden silinmiştir.
- **Zamansal Özellikler:** Yolculuğun tarih ve saat bilgisinden (`pickup_datetime`) **`year`**, **`month`**, **`day_of_week`** ve dakika/saniye hassasiyetli **`hour_numeric`** (ondalıklı saat) özellikleri çıkarılmıştır.

---

## 🔹 3. Kullanılan Modeller ve Optimizasyon

- **Modeller**
    - KNeighborsRegressor
    - DecisionTreeRegressor
    - AdaBoostRegressor
    - **RandomForestRegressor**
    - **XGBRegressor**
    - **LightGBM Regressor** ⬅️ *(En İyi Temel Performans)*
- **Hiperparametre Optimizasyonu:**
    - En iyi başlangıç skorunu veren **LightGBM** modeli üzerinde **GridSearchCV** ile hiperparametre optimizasyonu yapılmıştır.
    - **Hedef Skorlama:** RMSE'yi minimize etmek amacıyla `neg_root_mean_squared_error` metriği kullanılmıştır.

---

## ✅ Sonuçlar ve Performans Değerlendirmesi

Tüm modellerin karşılaştırmalı performans metrikleri (Test Seti):

| Model | RMSE (Hata) | R² Skoru | MAE (Ort. Mutlak Hata) |
| :--- | :--- | :--- | :--- |
| **LightGBM** | **4.0581** | **0.8142** | **2.0463** |
| **XGBoost** | 4.0836 | 0.8118 | 2.0540 |
| **Random Forest** | 4.1495 | 0.8057 | 2.1727 |
| Decision Tree | 4.2483 | 0.7964 | 2.1245 |
| AdaBoost | 5.8211 | 0.6177 | 3.9141 |
| KNeighbors | 9.6043 | -0.0405 | 5.9734 |

### 📈 Nihai Optimizasyon Sonuçları (LightGBM)

| Metrik | Değer |
| :--- | :--- |
| **Nihai RMSE** | **4.0559** |
| **Nihai R² Skoru** | **0.8144** |

```python
# En İyi Hiperparametreler (LightGBM - GridSearchCV)
# Bu parametreler ile model nihai skoru elde etmiştir.
{'learning_rate': 0.05, 'max_depth': -1, 'n_estimators': 200}
