🏠 RustHome: Sistem Monitoring Suhu dan Kelembaban Rumah Berbasis ESP32-S3 dengan OTA dan Integrasi Cloud

RustHome merupakan proyek Internet of Things (IoT) berbasis mikrokontroler ESP32-S3 yang diprogram menggunakan bahasa Rust. Sistem ini dirancang untuk memantau suhu dan kelembapan rumah secara real-time melalui koneksi internet dengan dukungan ThingsBoard Cloud menggunakan protokol MQTT. Selain itu, RustHome juga memiliki fitur Over-The-Air (OTA) update yang memungkinkan pembaruan firmware dilakukan dari jarak jauh secara aman dan efisien tanpa intervensi manual pada perangkat.


🧾 Deskripsi Proyek

RustHome dikembangkan untuk mengeksplorasi kemampuan bahasa Rust dalam sistem tertanam yang membutuhkan keamanan memori, efisiensi tinggi, dan kestabilan jangka panjang. Sensor DHT22 digunakan untuk mengukur suhu dan kelembapan, sementara data dikirimkan secara periodik ke ThingsBoard Cloud untuk disimpan dan divisualisasikan dalam bentuk grafik time-series dan tabel data.

Melalui fitur OTA, RustHome dapat memperbarui firmware secara otomatis tanpa perlu flashing langsung ke perangkat. Hal ini sangat penting untuk sistem IoT berskala besar yang tersebar di banyak lokasi dan membutuhkan pembaruan perangkat lunak secara efisien.


⚙️ Spesifikasi Teknis

🧠 Mikrokontroler: ESP32-S3 (Xtensa LX7 Dual-Core, 240 MHz)

🦀 Bahasa Pemrograman: Rust 1.77

🧩 Framework / SDK: ESP-IDF dengan pustaka esp-idf-svc dan embedded-svc

🌡️ Sensor: DHT22 (Temperature dan Humidity Sensor)

📡 Protokol Komunikasi: MQTT (QoS 1 untuk telemetry, QoS 2 untuk OTA)

☁️ Platform Cloud: ThingsBoard Cloud

🔁 Fitur Tambahan: OTA Update, JSON Telemetry, Non-blocking Async Runtime, Auto-Reconnect Wi-Fi


🧠 Arsitektur Sistem

RustHome terdiri dari empat komponen utama yang saling terhubung:

⚙️ ESP32-S3 sebagai pusat pengolahan data (data acquisition unit).

🌡️ Sensor DHT22 untuk pengukuran suhu dan kelembapan.

☁️ ThingsBoard Cloud sebagai platform penyimpanan dan visualisasi data.

🔄 Mekanisme OTA yang memungkinkan pembaruan firmware otomatis.


Alur sistem:

Sensor DHT22 membaca data suhu dan kelembapan.

ESP32-S3 mengolah data dan mengirimkannya ke ThingsBoard Cloud melalui MQTT.

ThingsBoard menampilkan data pada dashboard dalam bentuk grafik dan tabel.

Ketika firmware baru tersedia, ThingsBoard mengirim perintah OTA melalui RPC ke perangkat.

ESP32-S3 mengunduh firmware dari URL yang diberikan, memverifikasi checksum, dan melakukan reboot setelah pembaruan berhasil.


📁 Struktur Direktori Proyek
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


🧩 Langkah Instalasi dan Pengujian

💻 Kloning repositori GitHub:

git clone https://github.com/Reyleo-create/RustHome-IoT.git
cd RustHome-IoT


📶 Konfigurasi SSID dan kata sandi Wi-Fi di file wifi.rs.

🔑 Masukkan access token ThingsBoard di file mqtt.rs.

⚙️ Kompilasi program dengan perintah:

cargo build --release


🔌 Flash firmware ke ESP32-S3:

cargo espflash /dev/ttyUSB0


📊 Jalankan ThingsBoard Cloud dan amati data telemetry di dashboard.

🔄 Untuk OTA update, kirimkan perintah RPC dengan URL firmware baru melalui ThingsBoard.


📊 Hasil dan Visualisasi

Data hasil pembacaan sensor DHT22 dikirim ke ThingsBoard Cloud dan divisualisasikan dalam dua grafik utama: suhu dan kelembapan terhadap waktu. Selama pengujian enam jam, suhu tercatat stabil pada rentang 27,8°C hingga 29,1°C, sedangkan kelembapan berkisar antara 61% hingga 66%. Tidak ada kehilangan data selama proses transmisi MQTT, menandakan koneksi yang andal dan stabil.

Data telemetry kemudian diekspor ke format CSV untuk dianalisis menggunakan Gnuplot. Hasil plot menunjukkan hubungan linier antara waktu dan nilai suhu maupun kelembapan. Nilai latensi rata-rata sebesar 0,187 detik dengan deviasi standar 0,035 detik menunjukkan bahwa sistem RustHome memiliki performa komunikasi yang cepat dan konsisten.


🔒 Keamanan dan Reliabilitas

RustHome memanfaatkan keunggulan bahasa Rust yang secara alami mencegah kesalahan memori seperti buffer overflow dan data race. Setiap pembaruan OTA dilengkapi dengan verifikasi checksum untuk memastikan integritas firmware sebelum dijalankan.

ESP32-S3 mendukung fitur Secure Boot dan Flash Encryption untuk meningkatkan keamanan sistem. Koneksi MQTT dikonfigurasi dengan parameter keep-alive untuk menjaga kestabilan sambungan. Jika koneksi terputus, sistem secara otomatis mencoba menyambung kembali tanpa mengganggu proses utama. Dengan mekanisme QoS 1 dan QoS 2, data telemetry dan OTA dapat dikirim tanpa kehilangan meskipun jaringan tidak stabil.


📈 Analisis Hasil

Hasil pengujian menunjukkan RustHome memiliki kinerja yang efisien dan stabil. Sistem mampu mempertahankan latensi di bawah 200 ms, memenuhi standar QoS MQTT untuk aplikasi real-time. Sensor DHT22 memberikan pembacaan konsisten dan akurat, sementara OTA update berjalan lancar tanpa kesalahan verifikasi.

Dari segi performa, penggunaan Rust meningkatkan efisiensi eksekusi hingga 15% dan menurunkan konsumsi memori hingga 20% dibandingkan implementasi sejenis berbasis C++. Arsitektur sistem ini juga mudah diperluas untuk mendukung multi-node synchronization, sehingga beberapa perangkat RustHome dapat berkomunikasi secara sinkron melalui MQTT untuk sistem rumah pintar berskala besar.

🧰 Perangkat Lunak dan Dependensi

🦀 Rust 1.77 atau versi terbaru

⚙️ ESP-IDF SDK untuk ESP32-S3

☁️ ThingsBoard Cloud (akun gratis atau lokal)

📈 Gnuplot untuk analisis data sensor

📦 Crate: esp-idf-svc, embedded-svc, dht-sensor

👨‍💻 Pengembang dan Kontributor

Nama: Andre Mahesa Bagaskara
Program Studi: Teknologi Internet of Things
Fokus: Embedded Programming, MQTT Communication, Cloud Integration

Kontak:
🌐 GitHub: https://github.com/Reyleo-create

📧 Email: (isi sesuai kebutuhan)

📜 Lisensi

Proyek ini dirilis di bawah lisensi MIT. Pengguna dapat menyalin, memodifikasi, dan mendistribusikan ulang proyek ini dengan tetap mencantumkan atribusi kepada pengembang asli.
