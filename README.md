# TDoA Radio Transmitter Localization

Eksperimen sistem **Time Difference of Arrival (TDoA)** untuk memperkirakan lokasi pemancar radio menggunakan beberapa receiver RTL-SDR.

> Repository ini merupakan project eksperimen/kolaborasi. Beberapa komponen menggunakan kode atau referensi dari project SDR yang sudah ada. Pastikan atribusi dan kontribusi setiap anggota dijelaskan secara jujur.

## Gambaran Umum

Sistem ini memproses rekaman sinyal IQ dari tiga receiver untuk menghitung perbedaan waktu kedatangan sinyal (*time difference of arrival*). Perbedaan tersebut digunakan untuk membentuk kurva hiperbola dan memperkirakan posisi pemancar.

Alur utama sistem:

```text
RTL-SDR receivers
        |
        v
Synchronized IQ recordings
        |
        v
Signal reading and filtering
        |
        v
Cross-correlation / TDoA estimation
        |
        v
Hyperbola generation and multilateration
        |
        v
HTML map, heatmap, and estimated transmitter position
```

## Fitur

- Membaca data IQ hasil rekaman RTL-SDR.
- Mengolah sinyal dari tiga receiver.
- Melakukan korelasi sinyal untuk memperoleh estimasi TDoA.
- Menghasilkan hiperbola berdasarkan perbedaan jarak antarreceiver.
- Menghasilkan peta interaktif menggunakan Leaflet dan OpenStreetMap.
- Menjalankan evaluasi terhadap banyak file rekaman secara batch.
- Mengekstrak koordinat hasil dari banyak file HTML.
- Melakukan filtering berdasarkan kepadatan heatmap dan outlier.
- Membandingkan estimasi menggunakan median, weighted mean, DBSCAN, K-Means, dan consensus voting.
- Menghasilkan peta gabungan dengan titik receiver, target, reference, hasil estimasi, dan confidence score.

## Struktur Repository

```text
TDOA-MATHLAB/
├── evaluation_main.m          # Pipeline utama pemrosesan TDoA
├── run_batch_evaluation.m     # Menjalankan evaluasi banyak rekaman
├── extract_and_map_tdoa.py    # Ekstraksi, filtering, clustering, dan pembuatan peta
├── functions/                 # Fungsi MATLAB untuk korelasi, filtering, jarak, dan peta
├── recorded_data/             # Contoh atau hasil rekaman IQ
└── result/                    # Hasil peta dan evaluasi

tdoa-c/
├── src/                       # Eksperimen implementasi C/C++
└── lib/                       # Pembacaan dan filtering sinyal IQ

tdoa-pewaktu/
├── pewaktu-new.c              # Penjadwalan proses perekaman RTL-SDR
└── SYNC_REKAM.txt             # Contoh perintah perekaman pada beberapa node

smallrx-fm/
├── README.md                  # Dokumentasi receiver SDR
├── Makefile                   # Build program receiver
└── rad-new.c / rad.sh         # Eksperimen penerimaan dan demodulasi radio
```

## Teknologi

- MATLAB untuk pemrosesan sinyal dan perhitungan geometri TDoA.
- C/C++ untuk pembacaan dan filtering data IQ.
- Python untuk analisis hasil, filtering data, clustering, dan visualisasi.
- RTL-SDR sebagai perangkat penerima sinyal radio.
- Leaflet dan OpenStreetMap untuk visualisasi peta.
- NumPy, Pandas, SciPy, scikit-learn, dan BeautifulSoup untuk pemrosesan data Python.

## Menjalankan Evaluasi MATLAB

Pastikan MATLAB tersedia dan file rekaman IQ berada di folder yang sesuai dengan konfigurasi pada `TDOA-MATHLAB/evaluation_main.m`.

```matlab
cd TDOA-MATHLAB

evaluation_main('nama_file.dat')
```

Untuk menjalankan evaluasi terhadap banyak file:

```matlab
run_batch_evaluation
```

Sebelum menjalankan, periksa parameter berikut:

- posisi tiga receiver;
- posisi transmitter referensi;
- posisi target;
- nama folder data input;
- folder output;
- bandwidth filtering;
- tipe korelasi;
- mode peta.

## Menjalankan Analisis Python

Install dependency yang dibutuhkan:

```bash
pip install numpy pandas scipy scikit-learn beautifulsoup4
```

Kemudian sesuaikan konfigurasi `DIRECTORY` dan `PATTERN` di `TDOA-MATHLAB/extract_and_map_tdoa.py`, lalu jalankan:

```bash
cd TDOA-MATHLAB
python extract_and_map_tdoa.py
```

Output yang dapat dihasilkan antara lain:

- `tdoa_extracted_coordinates.csv`
- `tdoa_filtered_coordinates.csv`
- `skipped_files.csv`
- `combined_tdoa_results_with_estimates.html`

## Menjalankan Eksperimen C++

```bash
cd tdoa-c/src
make
./main
```

Program C++ menggunakan `ReadIQ` untuk membaca file biner IQ dan mengubah pasangan byte I/Q menjadi sinyal kompleks.

> Bagian C++ masih bersifat eksperimen dan mungkin membutuhkan penyesuaian nama header, path data, dan konfigurasi build sebelum dapat dijalankan pada lingkungan baru.

## Data Rekaman

Format input yang digunakan adalah rekaman IQ dari beberapa receiver. Contoh proses perekaman terdistribusi dapat dilihat pada `tdoa-pewaktu/SYNC_REKAM.txt`.

Jangan menyimpan credential, alamat jaringan internal, atau konfigurasi perangkat yang sensitif di repository publik.

## Kontribusi

Project ini dapat dikembangkan lebih lanjut dengan:

- merapikan konfigurasi agar tidak hard-coded;
- menambahkan `requirements.txt` atau `environment.yml`;
- memperbaiki build C/C++ menggunakan konfigurasi yang konsisten;
- menambahkan unit test untuk pembacaan IQ dan perhitungan TDoA;
- menambahkan dokumentasi format data rekaman;
- menghapus file hasil compile dan data mentah yang tidak diperlukan;
- menambahkan contoh output dan metrik akurasi yang terdokumentasi.

## Catatan untuk CV

Project ini dapat dicantumkan sebagai project team dengan deskripsi seperti berikut:

> Developed and evaluated a TDoA-based radio transmitter localization pipeline using three RTL-SDR receivers. Worked with MATLAB and Python for IQ signal processing, cross-correlation, hyperbolic positioning, outlier filtering, clustering, and map-based visualization.

Gunakan hanya bagian yang benar-benar sesuai dengan kontribusi pribadi. Jelaskan kontribusi secara spesifik, misalnya pengambilan data, konfigurasi receiver, pengembangan pipeline MATLAB, analisis Python, atau visualisasi hasil.

## Lisensi dan Atribusi

Belum ada lisensi repository yang ditentukan. Tambahkan file `LICENSE` jika project akan didistribusikan secara resmi. Periksa juga atribusi pada komponen SDR yang berasal dari project eksternal sebelum menggunakan atau mempublikasikan ulang kode tersebut.
