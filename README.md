

https://github.com/user-attachments/assets/086ec294-8c9b-49c0-a1e6-b5f1547f9828

# 🩺 AI Destekli Cilt Lezyonu Analiz ve Uyarı Sistemi (ESP32-S3 & Colab)

Bilgisayar Mühendisliği 3. sınıf projelerimden biri olan bu çalışmada; yapay zeka (Computer Vision) ile gömülü sistemleri (IoT) Wi-Fi üzerinden ücretsiz ve anlık olarak nasıl haberleştirebileceğimizi gösterdim.

Sistem özetle şöyle çalışıyor: Telefonda çekilen bir ben/sivilce fotoğrafını Google Colab'daki yapay zeka modeline veriyoruz. Model kanser riskini hesaplıyor ve sonucu MQTT protokolü üzerinden havadan ESP32-S3'e fırlatıyor. ESP32 ise bu veriyi alıp saniyeler içinde OLED ekranda grafiksel bir risk barı ile bize gösteriyor.

## 🛠️ Kullanılan Teknolojiler & Donanım
* **Mikrodenetleyici:** ESP32-S3 (Wi-Fi için)
* **Ekran:** 0.96 inç I2C OLED
* **Geliştirme Ortamı:** VS Code + ESP-IDF v6.1 (Saf C Dili)
* **Yapay Zeka Tarafı:** Google Colab, Python, HuggingFace Transformers (Vision Transformer)
* **Haberleşme:** Ücretsiz HiveMQ Broker (MQTT Protokolü)

## 🔌 Bağlantı Şeması 
* **OLED VCC**  ➔ ESP32 **3V3**
* **OLED GND**  ➔ ESP32 **GND**
* **OLED SDA**  ➔ ESP32 **GPIO 17**
* **OLED SCL**  ➔ ESP32 **GPIO 18**

## 🐛 Karşılaştığım Krizler ve Çözümlerim 

Hazır kütüphanelerle "tak-çalıştır" yapmak yerine sistemi sıfırdan kurarken bayağı bir sorunla boğuştum ve şunları öğrendim:

1. **ESP-IDF v6.1 Kütüphane Cehennemi:**
   Güncel ESP-IDF sürümünde `driver/i2c.h` ve `mqtt_client.h` kütüphaneleri sürekli derleme (CMakeLists) hatası verdi ve `exit code: 1` ile patladı. İnternetten hazır kütüphane aramak yerine; OLED ekran için **kendi saf GPIO (Bit-Banging) I2C sürücümü** yazdım. MQTT bağlantısı için de paket kullanmak yerine doğrudan **LwIP TCP Soketleri** üzerinden kendi MQTT paketlerimi (CONNECT, SUBSCRIBE, PUBLISH) manuel olarak C dilinde bit bit inşa ettim.
2. **Yapay Zekanın "Her Şeye Kanser" Demesi (Domain Gap):**
   Başlarda kendi kolumdaki beni çektiğimde model sürekli %95 kanser riski diyordu. Sonra fark ettim ki model hastanedeki profesyonel dermatoskop cihazlarının (sıfır gölge, sadece lezyonu gösteren) fotoğraflarıyla eğitilmiş. Telefonla uzaktan ve odanın sarı ışığında çekince model afallıyordu. Çözüm olarak; fotoğrafları flaşsız gün ışığında çekip, sadece "ben" kısmını kare olarak kırparak  modele vermeye başladım. Sonuçlar anında tutarlı hale geldi.

## 🚀 Sistemi Nasıl Çalıştırırsınız?
1. `main.c` içindeki `WIFI_ADI` ve `WIFI_SIFRESI` kısımlarını kendi modeminize göre doldurun ve ESP32'ye flashlayın. Ekranda "COLAB BEKLENIYOR" yazısını görün.
2. Colab dosyasındaki Python kodunu çalıştırın. Kırpılmış, net bir lezyon fotoğrafı yükleyin.
3. Colab "Analiz Tamamlandı" dediği an OLED ekrana risk yüzdesi düşecektir!

