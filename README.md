# LK-02-Eksplorasi-Ekosistem-Minilab-Big-Data-1-2-
Fadilah Puji Laily Maulidiyah_245150401111008

**1. Prasyarat Sistem**
      Sebelum memulai proses, saya memastikan perangkat telah memenuhi kriteria berikut:
      **Docker Desktop:** Dalam status aktif dan berjalan (*running*).
      **Python 3.10+:** Terinstal pada sistem host untuk pengelolaan lingkungan virtual.
      **Ketersediaan Port:** Memastikan port `5432` (PostgreSQL), `9000` (MinIO API), dan `9001` (MinIO Console) tidak sedang digunakan oleh layanan lain.

**2. Konfigurasi Awal (Setup)**
      Saya melakukan inisialisasi lingkungan melalui Windows PowerShell di direktori utama proyek dengan langkah-langkah berikut:
    **A. Menyiapkan Environment Variable**
          Saya menyalin file template `.env.example` menjadi `.env` untuk mengatur kredensial basis data dan storage:
          
         Copy-Item .env.example .env
          
   ** B. Menjalankan Infrastruktur Docker**
          Saya mengaktifkan layanan PostgreSQL, MinIO, dan MinIO Client di latar belakang menggunakan perintah:
        
         docker compose up -d
          
          Saya menggunakan perintah `docker compose ps` untuk memastikan seluruh kontainer telah berstatus Healthy.

**3. Lingkungan Virtual dan Dependensi**
      Untuk menjaga isolasi pustaka Python, saya melakukan konfigurasi lingkungan virtual sebagai berikut:
      **A. Pembuatan Virtual Environment**
      
      python -m venv .venv
      
      **B. Instalasi Dependensi**
      Guna menghindari batasan keamanan Execution Policy pada PowerShell, saya menginstal pustaka yang dibutuhkan langsung melalui executable Python di dalam folder `.venv`:
      
      .\.venv\Scripts\python.exe -m pip install -r requirements.txt

**4. Menjalankan Pipeline Ingestion**
      Saya mengeksekusi skrip utama untuk menarik data dari PostgreSQL dan file lokal menuju MinIO:

      .\.venv\Scripts\python.exe -m ingestion.main_ingest
      
       Proses Ingestion yang Saya Lakukan:
        1.  Ekstraksi: Mengambil data dari tabel `customers` dan `orders` di PostgreSQL.
        2.  Validasi: Memverifikasi kualitas data menggunakan modul `validator.py`.
        3.  Standardisasi: Menambahkan metadata waktu ingestion dan informasi tipe sumber.
        4.  Pemuatan: Mengunggah file `.csv` hasil olahan ke bucket `datalake` di MinIO pada zona `raw/`.

**5. Verifikasi dan Pengujian**
    **A. Verifikasi Melalui MinIO Console**
    Saya melakukan pengecekan hasil melalui browser di alamat [http://localhost:9001](https://www.google.com/search?q=http://localhost:9001) dengan kredensial `minioadmin` (sesuai konfigurasi `.env`). Saya memastikan file telah tersimpan pada bucket `datalake/raw/`.
    
    **B. Menjalankan Unit Test**
    Saya memastikan logika aplikasi berjalan sesuai spesifikasi dengan menjalankan pengujian otomatis:
    
    .\.venv\Scripts\python.exe -m pytest tests/ -v

**6. Struktur Output pada Data Lake**
      Data yang saya proses tersimpan dengan hierarki sebagai berikut:
        * `datalake/raw/rdbms/` : Hasil ekstraksi tabel PostgreSQL.
        * `datalake/raw/csv/` : Hasil pengolahan file CSV lokal.
        * `datalake/raw/xlsx/` : Hasil pengolahan file Excel lokal.
