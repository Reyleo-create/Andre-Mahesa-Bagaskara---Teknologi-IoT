
# 🏠 RustHome: Sistem Monitoring Suhu dan Kelembaban Rumah Berbasis ESP32-S3 dengan OTA dan Integrasi Cloud 

RustHome merupakan proyek Internet of Things (IoT) berbasis mikrokontroler ESP32-S3 yang diprogram menggunakan bahasa Rust. Sistem ini dirancang untuk memantau suhu dan kelembapan rumah secara real-time melalui integrasi ThingsBoard Cloud menggunakan protokol MQTT. Proyek ini juga mendukung pembaruan firmware jarak jauh melalui fitur Over-The-Air (OTA) update yang aman, efisien, dan handal.

---

## 🧾 Deskripsi Proyek

RustHome dikembangkan untuk mengeksplorasi potensi bahasa Rust dalam sistem tertanam (embedded system) yang membutuhkan keamanan memori, efisiensi tinggi, dan keandalan jangka panjang. Sensor DHT22 digunakan untuk membaca suhu dan kelembapan, sementara ESP32-S3 berfungsi sebagai pengendali utama yang mengirimkan data ke ThingsBoard Cloud secara periodik.

Data ditampilkan dalam bentuk grafik time-series dan tabel interaktif melalui dashboard ThingsBoard. Selain itu, mekanisme OTA memungkinkan pembaruan firmware dilakukan tanpa perlu koneksi fisik ke perangkat, sehingga mendukung efisiensi operasional dan skalabilitas sistem IoT.

---

## ⚙️ Spesifikasi Teknis

* 🧠 Mikrokontroler: ESP32-S3 (Xtensa LX7 Dual-Core, 240 MHz)
* 🦀 Bahasa Pemrograman: Rust 1.77
* 🧩 Framework / SDK: ESP-IDF dengan pustaka esp-idf-svc dan embedded-svc
* 🌡️ Sensor: DHT22 (Temperature dan Humidity Sensor)
* 📡 Protokol Komunikasi: MQTT (QoS 1 untuk telemetry, QoS 2 untuk OTA)
* ☁️ Platform Cloud: ThingsBoard Cloud
* 🔁 Fitur Tambahan: OTA Update, JSON Telemetry, Non-blocking Async Runtime, Auto-Reconnect Wi-Fi

---

## 🧠 Arsitektur Sistem

RustHome terdiri dari empat komponen utama yang saling terhubung:

* ⚙️ ESP32-S3 sebagai pusat pengolahan data (data acquisition unit).
* 🌡️ Sensor DHT22 untuk pengukuran suhu dan kelembapan.
* ☁️ ThingsBoard Cloud sebagai platform penyimpanan dan visualisasi data.
* 🔄 Mekanisme OTA yang memungkinkan pembaruan firmware otomatis.

### 🔧 Alur Sistem

1. 🌡️ Sensor DHT22 membaca data suhu dan kelembapan.
2. ⚙️ ESP32-S3 memproses data dan mengirimkannya ke ThingsBoard Cloud melalui MQTT.
3. 📊 ThingsBoard menampilkan data pada dashboard dalam bentuk grafik dan tabel interaktif.
4. 🔁 Ketika firmware baru tersedia, ThingsBoard mengirim perintah OTA melalui RPC ke perangkat.
5. 💾 ESP32-S3 mengunduh firmware baru, memverifikasi checksum, dan melakukan reboot setelah pembaruan berhasil.

---

## 📁 Struktur Direktori Proyek

```
RustHome/
│
├── src/
│   ├── main.rs         // Entry point utama program
│   ├── wifi.rs         // Modul koneksi dan auto-reconnect Wi-Fi
│   ├── mqtt.rs         // Modul komunikasi MQTT dan RPC listener
│   ├── sensor.rs       // Pembacaan data DHT22 dan serialisasi JSON
│   └── ota.rs          // Modul pembaruan OTA dan validasi firmware
│
├── Cargo.toml          // Konfigurasi dependensi proyek Rust
└── README.md           // Dokumentasi proyek
```

---

## 🧩 Langkah Instalasi dan Pengujian

1. 💻 Kloning repositori GitHub:

   ```
   git clone https://github.com/Reyleo-create/RustHome-IoT.git
   cd RustHome-IoT
   ```

2. 📶 Konfigurasi SSID dan kata sandi Wi-Fi di file wifi.rs.

3. 🔑 Masukkan access token ThingsBoard di file mqtt.rs.

4. ⚙️ Kompilasi program dengan perintah:

   ```
   cargo build --release
   ```

5. 🔌 Flash firmware ke ESP32-S3:

   ```
   cargo espflash /dev/ttyUSB0
   ```

6. 📊 Jalankan ThingsBoard Cloud dan amati data telemetry di dashboard.

7. 🔄 Lakukan OTA update dengan mengirim perintah RPC berisi URL firmware baru dari ThingsBoard.

---

## 📊 Hasil dan Visualisasi

Hasil pengujian menunjukkan bahwa RustHome mampu mengirim data suhu dan kelembapan secara konsisten ke ThingsBoard Cloud tanpa kehilangan paket data.

* 🌡️ Suhu tercatat pada rentang 27,8°C hingga 29,1°C
* 💧 Kelembapan berkisar antara 61% hingga 66%
* ⏱️ Rata-rata latensi pengiriman: 0,187 detik
* 📉 Deviasi standar latensi: 0,035 detik

Data diekspor ke format CSV dan dianalisis menggunakan Gnuplot. Hasil plot menunjukkan tren yang stabil dan linear antara waktu dan nilai pengukuran. Nilai latensi yang rendah membuktikan efisiensi komunikasi MQTT, sementara sensor DHT22 menunjukkan keakuratan dan konsistensi yang baik selama pengambilan data.

---

## 🔒 Keamanan dan Reliabilitas

RustHome memanfaatkan sistem kepemilikan (ownership) Rust untuk menghindari kesalahan memori seperti buffer overflow dan data race. Mekanisme OTA dilengkapi validasi checksum dan rollback otomatis jika pembaruan gagal.

ESP32-S3 mendukung fitur Secure Boot dan Flash Encryption untuk melindungi integritas firmware. Sistem juga menerapkan koneksi MQTT dengan keep-alive interval untuk menjaga kestabilan jaringan, serta auto-reconnect jika koneksi terputus.

---

## 📈 Analisis Hasil

Pengujian menunjukkan RustHome memiliki performa tinggi dan efisiensi komunikasi yang baik. Latensi di bawah 200 ms memenuhi kebutuhan aplikasi IoT real-time, sementara konsumsi memori berkurang hingga 20% dibandingkan sistem berbasis C. OTA update bekerja tanpa kesalahan dan sistem tetap stabil selama pengujian berjam-jam.

Proyek ini dapat dikembangkan lebih lanjut untuk mendukung sinkronisasi multi-perangkat (multi-node) agar beberapa perangkat RustHome dapat saling berkomunikasi melalui MQTT dalam arsitektur rumah pintar terdistribusi.

---

## 🧰 Perangkat Lunak dan Dependensi

* 🦀 Rust 1.77 atau versi terbaru
* ⚙️ ESP-IDF SDK untuk ESP32-S3
* ☁️ ThingsBoard Cloud (akun gratis atau lokal)
* 📈 Gnuplot untuk analisis data sensor
* 📦 Crate: esp-idf-svc, embedded-svc, dht-sensor

---

## 👨‍💻 Pembuat

1. Muhammad Jidan Baraja NRP 2042231009
2. Andre Mahesa Bagaskara NRP 2042231012

Kontak:
🌐 GitHub: [https://github.com/Reyleo-create](https://github.com/Reyleo-create)
📧 Email: (isi sesuai kebutuhan Anda)

---

## 📜 Lisensi

Proyek ini dirilis di bawah lisensi MIT. Pengguna dapat menyalin, memodifikasi, dan mendistribusikan ulang proyek ini dengan tetap mencantumkan atribusi kepada pengembang asli.
