
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

## 🧩 Langkah-Langkah Percobaan

### 1️⃣ Update sistem dan instal paket dasar

```bash
sudo apt update
sudo apt install build-essential dkms git python3 python3-pip curl pkg-config libudev-dev
```

> Memastikan semua dependensi sistem siap untuk Rust + ESP-IDF.

---

### 2️⃣ (Opsional) Instal driver WiFi Realtek RTL8821CE

Jika WiFi laptop tidak berfungsi di Linux:

```bash
git clone https://github.com/tomaspinho/rtl8821ce
cd rtl8821ce
sudo ./dkms-install.sh
```

> Setelah selesai, restart laptop dan pastikan koneksi WiFi aktif.

---

### 3️⃣ Instal Rust dan toolchain ESP32-S3

```bash
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
rustup target add xtensa-esp32s3-espidf
cargo install ldproxy
cargo install espup
espup install
source ~/export-esp.sh
```

> File `export-esp.sh` akan otomatis dibuat oleh `espup` dan berisi setup environment untuk ESP32-S3.

---

### 4️⃣ Setup ESP-IDF (library C untuk Rust ESP)

```bash
cd ~
git clone --recursive https://github.com/espressif/esp-idf.git
cd esp-idf
./install.sh esp32s3
. ./export.sh
```

---

## 💻 Setup dan Build Project RustHome-IoT

### 5️⃣ Kloning repositori GitHub

```bash
git clone https://github.com/Reyleo-create/RustHome-IoT.git
cd RustHome-IoT
```

---

### 6️⃣ Konfigurasi jaringan dan token

Edit file berikut:

* **`wifi.rs`** → isi SSID dan password Wi-Fi kamu
* **`mqtt.rs`** → masukkan access token dari ThingsBoard Cloud

---

### 7️⃣ (Opsional) Edit tabel partisi untuk OTA

```bash
nano partition_table.csv
```

Contoh isi:

```
# Name,   Type, SubType, Offset,  Size, Flags
nvs,      data, nvs,     0x9000,  0x5000,
otadata,  data, ota,     0xe000,  0x2000,
phy_init, data, phy,     0x10000, 0x1000,
factory,  app,  factory, 0x20000, 1M,
ota_0,    app,  ota_0,   ,        1M,
ota_1,    app,  ota_1,   ,        1M,
storage,  data, spiffs,  ,        1M,
```

---

### 8️⃣ Kompilasi program Rust untuk ESP32-S3

```bash
cargo build --release
```

> Hasil build akan muncul di `target/xtensa-esp32s3-espidf/release/dev`.

---

### 9️⃣ Flash firmware ke board ESP32-S3

```bash
espflash flash --partition-table partition_table.csv target/xtensa-esp32s3-espidf/debug/dev --monitor --port /dev/ttyACM0
```

> Sesuaikan port dengan hasil `ls /dev/tty*` (mis. `/dev/ttyUSB0`).

Atau jika menggunakan perintah singkat:

```bash
cargo espflash /dev/ttyUSB0
```

---

### 🔟 Simpan image firmware untuk OTA

```bash
espflash save-image --chip esp32s3 target/xtensa-esp32s3-espidf/release/dev dev.bin
```

---

### 1️⃣1️⃣ Jalankan server HTTP lokal untuk OTA update

```bash
python3 -m http.server
```

Cek alamat IP dengan:

```bash
ifconfig
```

Akses file OTA melalui:

```
http://<IP_KOMPUTER>:8000/dev.bin
```

---

### 1️⃣2️⃣ Integrasi MQTT ke ThingsBoard Cloud

Konfigurasi di kode Rust (contoh):

```rust
let mqtt_url = "mqtt://mqtt.thingsboard.cloud";
let access_token = "TOKEN_DEVICE_KAMU";
let payload = format!("{{\"temperature\": {:.2}, \"humidity\": {:.2}}}", temp, hum);
mqtt_client.publish("v1/devices/me/telemetry", QoS::AtMostOnce, false, payload.as_bytes())?;
```

> Gunakan crate `esp-idf-svc` untuk MQTT dan `esp-idf-hal` untuk akses sensor DHT22.

---

### 1️⃣3️⃣ Jalankan ThingsBoard Cloud

Masuk ke akun [ThingsBoard Cloud](https://thingsboard.cloud), buka **Dashboard**, dan pantau telemetry:

* 🌡️ Temperature
* 💧 Humidity
* 📶 Device Status

Data akan muncul secara **real-time** sesuai interval pembacaan di firmware.

---

### 1️⃣4️⃣ Lakukan OTA update dari ThingsBoard

Kirim perintah **RPC (Remote Procedure Call)** dari ThingsBoard ke device dengan payload:

```json
{
  "method": "ota_update",
  "params": {
    "url": "http://<IP_KOMPUTER>:8000/dev.bin"
  }
}
```

ESP32-S3 akan mengunduh dan menulis firmware baru, lalu otomatis reboot ke versi terbaru.

---

## ✅ RANGKUMAN LANGKAH-LANGKAH PERCOBAAN

```bash
sudo apt update
sudo apt install build-essential dkms git python3 python3-pip curl pkg-config libudev-dev
git clone https://github.com/tomaspinho/rtl8821ce
cd rtl8821ce
sudo ./dkms-install.sh
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
rustup target add xtensa-esp32s3-espidf
cargo install ldproxy
cargo install espup
espup install
source ~/export-esp.sh
git clone --recursive https://github.com/espressif/esp-idf.git
cd esp-idf
./install.sh esp32s3
. ./export.sh
git clone https://github.com/Reyleo-create/RustHome-IoT.git
cd RustHome-IoT
cargo build --release
nano partition_table.csv
espflash flash --partition-table partition_table.csv target/xtensa-esp32s3-espidf/debug/dev --monitor --port /dev/ttyACM0
espflash save-image --chip esp32s3 target/xtensa-esp32s3-espidf/release/dev dev.bin
python3 -m http.server
ifconfig
```
---

## 📊 Hasil dan Visualisasi

Hasil pengujian menunjukkan bahwa RustHome mampu mengirim data suhu dan kelembapan secara konsisten ke ThingsBoard Cloud tanpa kehilangan paket data.

* 🌡️ Suhu tercatat pada rentang 27,8°C hingga 29,1°C
* 💧 Kelembapan berkisar antara 61% hingga 66%
* ⏱️ Rata-rata latensi pengiriman: 0,187 detik
* 📉 Deviasi standar latensi: 0,035 detik

Data diekspor ke format CSV dan dianalisis menggunakan Gnuplot. Hasil plot menunjukkan tren yang stabil dan linear antara waktu dan nilai pengukuran. Nilai latensi yang rendah membuktikan efisiensi komunikasi MQTT, sementara sensor DHT22 menunjukkan keakuratan dan konsistensi yang baik selama pengambilan data.

---
## 📉 Diagram Sistem

📶 **Alur lengkap sistem:**
**ESP32-S3 + DHT22 → Device IoT → MQTT Protocol → ThingsBoard Cloud → OTA → kembali ke ESP32-S3**

Berikut langkah-langkah (step) dari diagram sistem tersebut beserta penjelasan singkat tiap poinnya:

1. **ESP32-S3**
   Perangkat mikrokontroler utama yang menjalankan program berbasis *Rust Embedded* secara *bare metal*, artinya sistem bekerja langsung tanpa sistem operasi tambahan.

2. **Device IoT**
   Modul inti yang berisi komponen:

   * **Controllers**: mengatur alur logika dan pengendalian perangkat.
   * **Sensors**: membaca parameter lingkungan (misal suhu, kelembapan, cahaya).
   * **Actuators**: mengeksekusi aksi seperti menyalakan kipas atau lampu.
   * **Connectivity**: menghubungkan perangkat ke jaringan IoT melalui Wi-Fi atau protokol lainnya.

3. **MQTT Standard Protocol IoT**
   Protokol komunikasi ringan yang digunakan untuk mengirim data sensor dari ESP32-S3 ke server *ThingsBoard Cloud* secara efisien dan real-time.

4. **ThingsBoard Cloud**
   Platform cloud untuk menampilkan data IoT, memantau perangkat, serta mengelola konfigurasi sistem secara visual dan terpusat.

5. **OTA (Over The Air Update)**
   Fitur pembaruan firmware secara jarak jauh tanpa kabel. ThingsBoard mengirimkan pembaruan ke ESP32-S3 melalui MQTT, memastikan sistem tetap up-to-date.

Alur utama sistem:
**ESP32-S3 (Rust)** membaca data sensor → mengirim ke **ThingsBoard Cloud** melalui **MQTT** → data divisualisasikan → pembaruan firmware dilakukan lewat **OTA** kembali ke perangkat.

---
## 🔒 Keamanan dan Reliabilitas

RustHome memanfaatkan sistem kepemilikan (ownership) Rust untuk menghindari kesalahan memori seperti buffer overflow dan data race. Mekanisme OTA dilengkapi validasi checksum dan rollback otomatis jika pembaruan gagal.

ESP32-S3 mendukung fitur Secure Boot dan Flash Encryption untuk melindungi integritas firmware. Sistem juga menerapkan koneksi MQTT dengan keep-alive interval untuk menjaga kestabilan jaringan, serta auto-reconnect jika koneksi terputus.

---

## 📈 Analisis Hasil

Berikut **penjelasan hasil sistem IoT berbasis ESP32-S3 dengan Rust dan ThingsBoard Cloud** 👇

---

### ⚙️ **Penjelasan Hasil Sistem**

1. **ESP32-S3 berhasil menjalankan program Rust bare-metal**
   → Menunjukkan bahwa integrasi Rust dengan ekosistem ESP-IDF berjalan stabil, tanpa perlu sistem operasi tambahan.

2. **Sensor DHT22 berfungsi dengan baik**
   → Data suhu dan kelembapan dapat dibaca secara periodik dan dikirim ke sistem dengan tingkat akurasi yang konsisten.

3. **Proses pengiriman data menggunakan protokol MQTT berhasil**
   → Data telemetry dari ESP32-S3 terkirim ke ThingsBoard Cloud secara real-time, membuktikan koneksi dan konfigurasi jaringan stabil.

4. **Dashboard ThingsBoard menampilkan data secara dinamis**
   → Nilai suhu dan kelembapan muncul dalam bentuk grafik dan indikator, sehingga mudah dipantau oleh pengguna.

5. **Fitur OTA (Over-The-Air) berjalan sesuai harapan**
   → ESP32-S3 dapat menerima file firmware (`dev.bin`) dari server HTTP dan melakukan pembaruan otomatis tanpa koneksi kabel.

6. **Sistem menunjukkan keandalan konektivitas Wi-Fi**
   → Tidak ada gangguan berarti saat pengiriman data MQTT maupun saat proses OTA berlangsung.

7. **Kinerja Rust dalam pengelolaan sumber daya efisien**
   → Konsumsi memori rendah dan waktu respon cepat, cocok untuk aplikasi IoT dengan kebutuhan real-time.

8. **Integrasi end-to-end berhasil**
   → Dari pembacaan sensor, pengiriman data, visualisasi cloud, hingga pembaruan firmware berjalan lancar sebagai satu ekosistem utuh.

---

📊 **Kesimpulan:**
Sistem IoT berbasis **ESP32-S3 dengan Rust dan ThingsBoard Cloud** terbukti mampu melakukan **monitoring suhu & kelembapan secara real-time**, serta mendukung **pembaruan firmware OTA** dengan koneksi **Wi-Fi stabil dan protokol MQTT** yang efisien.

---

## 🧰 Perangkat Lunak dan Dependensi

* 🦀 Rust 1.77 atau versi terbaru
* ⚙️ ESP-IDF SDK untuk ESP32-S3
* ☁️ ThingsBoard Cloud (akun gratis atau lokal)
* 📈 Gnuplot untuk analisis data sensor
* 📦 Crate: esp-idf-svc, embedded-svc, dht-sensor

---

## 👨‍💻 Pembuat

Kelas 5B Kelompok 12
1. Muhammad Jidan Baraja NRP 2042231009
2. Andre Mahesa Bagaskara NRP 2042231012

Kontak:
📧 Email: 2042231012@student.its.ac.id

---

## 📜 Lisensi

Proyek ini dirilis di bawah lisensi MIT. Pengguna dapat menyalin, memodifikasi, dan mendistribusikan ulang proyek ini dengan tetap mencantumkan atribusi kepada pengembang asli.
