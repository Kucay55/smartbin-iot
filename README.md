# smartbin-iot
# 🗑️ Smart Bin IoT – Real-Time Waste Monitoring

![Platform](https://img.shields.io/badge/platform-ESP32%20%7C%20Python%20%7C%20Firebase-blue)
![Status](https://img.shields.io/badge/status-experimental-yellow)
![License](https://img.shields.io/badge/license-MIT-green)

Sistem Smart Bin ini dirancang untuk memantau kapasitas tempat sampah secara real-time menggunakan sensor ultrasonik dan konektivitas cloud. Cocok untuk diterapkan di rumah sakit, ruang publik, dan fasilitas layanan masyarakat.

---

## 🚀 Tujuan Eksperimen

- Deteksi tingkat kepenuhan tempat sampah menggunakan sensor ultrasonik
- Pengiriman data ke Firebase secara berkala via Wi-Fi
- Visualisasi status bin melalui dashboard web
- Integrasi antara perangkat keras, perangkat lunak, dan cloud

---
Real-time Smart Bin IoT system for waste monitoring using ESP32, Firebase, and Web Dashboard
SmartBin-IoT/ ├── Arduino/ │   └── smart_bin.ino             # Kode mikrokontroler untuk membaca sensor dan kirim data ke Firebase ├── Python/ │   └── firebase_push.py          # Skrip Python untuk simulasi pengiriman data ke Firebase ├── Dashboard/ │   ├── index.html                # Tampilan dashboard web │   └── script.js                 # Logika pengambilan data dari Firebase ├── Docs/ │   ├── architecture_diagram.png # Diagram arsitektur sistem │   └── flowchart.pdf             # Alur kerja sistem ├── README.md                     # Penjelasan umum eksperimen dan struktur file └── requirements.txt              # Dependensi Python

---

## ⚙️ Cara Menjalankan

### 1. Perangkat Keras
- Upload `smart_bin.ino` ke ESP32
- Pastikan sensor ultrasonik terhubung ke pin yang sesuai

### 2. Simulasi Python (Opsional)
```bash
'databaseURL': 'https://smartbin-iot-default-rtdb.firebaseio.com'
firebase-admin==6.0.1
requests==2.31.0
pip install -r requirements.txt
python firebase_push.py

Dashboard Web
- Buka Dashboard/index.html di browser
- Pastikan konfigurasi Firebase sudah diatur di script.js


🔐 Konfigurasi Firebase
Pastikan Anda sudah membuat project Firebase dan mendapatkan:
- apiKey
- authDomain
- databaseURL
- projectId
Masukkan konfigurasi tersebut ke dalam file:
- smart_bin.ino
- firebase_push.py
- script.js


🔗 URL Repositori
 https://github.com/kucay55/smartbin-iot

import firebase_admin
from firebase_admin import credentials, db
import time
from datetime import datetime
import random

# 🔐 Inisialisasi Firebase
cred = credentials.Certificate("serviceAccountKey.json")
firebase_admin.initialize_app(cred, {
    'databaseURL': 'https://your-project-id.firebaseio.com'  # Ganti dengan URL Firebase kamu
})

# 🚮 Fungsi untuk simulasi status bin
def get_bin_status():
    return random.choice(["empty", "half", "full"])

# 🚀 Loop pengiriman data
while True:
    status = get_bin_status()
    timestamp = datetime.now().isoformat()

    data = {
        "bin_status": status,
        "timestamp": timestamp
    }

    ref = db.reference("/smartbin")
    ref.set(data)

    print(f"[{timestamp}] Status dikirim: {status}")
    time.sleep(10)  # Kirim setiap 10 detik

SmartBin-IoT/
├── Python/
│   ├── firebase_push.py
│   ├── serviceAccountKey.json
│   └── requirements.txt

import firebase_admin
from firebase_admin import credentials, db
import serial
from datetime import datetime

# 🔐 Inisialisasi Firebase
cred = credentials.Certificate("serviceAccountKey.json")
firebase_admin.initialize_app(cred, {
    'databaseURL': 'https://your-project-id.firebaseio.com'  # Ganti dengan URL Firebase kamu
})

# 🔌 Inisialisasi koneksi serial ke ESP32
try:
    ser = serial.Serial('COM3', 9600, timeout=2)  # Ganti COM3 sesuai port kamu
    print("Serial connection established.")
except Exception as e:
    print("Gagal membuka koneksi serial:", e)
    exit()

# 🚀 Loop pembacaan dan pengiriman data
while True:
    try:
        line = ser.readline().decode('utf-8').strip()
        if line:
            # Contoh data dari ESP32: "full" atau "empty"
            status = line
            timestamp = datetime.now().isoformat()

            data = {
                "bin_status": status,
                "timestamp": timestamp
            }

            ref = db.reference("/smartbin")
            ref.set(data)

            print(f"[{timestamp}] Status dikirim: {status}")
    except Exception as e:
        print("Error saat membaca atau mengirim data:", e)

