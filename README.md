
# LK-02 — Eksplorasi Ekosistem Minilab Big Data 1/2

**Nama / NIM:** Fadilah Puji Laily Maulidiyah — 245150401111008

---

## 1. Prasyarat Sistem

Sebelum memulai proses, pastikan perangkat memenuhi kriteria berikut:

- **Docker Desktop**: status aktif dan berjalan (*running*).
- **Python 3.10+**: terinstal pada sistem host untuk pengelolaan *virtual environment*.
- **Ketersediaan port**: pastikan port berikut tidak digunakan layanan lain:
  - `5432` (PostgreSQL)
  - `9000` (MinIO API)
  - `9001` (MinIO Console)

---

## 2. Konfigurasi Awal (Setup)

Inisialisasi lingkungan dilakukan melalui **Windows PowerShell** pada direktori utama proyek.

### A. Menyiapkan Environment Variable

Salin template `.env.example` menjadi `.env` untuk mengatur kredensial basis data dan storage:

```powershell
Copy-Item .env.example .env
```

### B. Menjalankan Infrastruktur Docker

Aktifkan layanan PostgreSQL, MinIO, dan MinIO Client di latar belakang:

```powershell
docker compose up -d
```

Pastikan seluruh kontainer berstatus **Healthy**:

```powershell
docker compose ps
```

---

## 3. Virtual Environment dan Dependensi

Untuk menjaga isolasi pustaka Python, lakukan konfigurasi berikut.

### A. Membuat Virtual Environment

```powershell
python -m venv .venv
```

### B. Menginstal Dependensi

Untuk menghindari batasan keamanan **Execution Policy** pada PowerShell, instal pustaka menggunakan executable Python di dalam folder `.venv`:

```powershell
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

---

## 4. Menjalankan Pipeline Ingestion

Jalankan skrip utama untuk menarik data dari PostgreSQL dan file lokal menuju MinIO:

```powershell
.\.venv\Scripts\python.exe -m ingestion.main_ingest
```

### Tahapan ingestion yang dilakukan

- **Ekstraksi**: mengambil data dari tabel `customers` dan `orders` di PostgreSQL.
- **Validasi**: memverifikasi kualitas data menggunakan modul `validator.py`.
- **Standardisasi**: menambahkan metadata waktu ingestion dan informasi tipe sumber.
- **Pemuatan**: mengunggah file `.csv` hasil olahan ke bucket `datalake` di MinIO pada zona `raw/`.

---

## 5. Verifikasi dan Pengujian

### A. Verifikasi melalui MinIO Console

Buka melalui browser:

- **URL:** `http://localhost:9001`
- **Kredensial:** `minioadmin` (sesuai konfigurasi `.env`)

Pastikan file sudah tersimpan di bucket `datalake` pada path yang sesuai (misalnya di zona `raw/`).

### B. Menjalankan Unit Test

Jalankan pengujian otomatis untuk memastikan logika aplikasi sesuai spesifikasi:

```powershell
.\.venv\Scripts\python.exe -m pytest tests/ -v
```

---

## 6. Struktur Output pada Data Lake

Data yang diproses tersimpan dengan hierarki berikut:

- `datalake/raw/rdbms/` : hasil ekstraksi tabel PostgreSQL
- `datalake/raw/csv/` : hasil pengolahan file CSV lokal
- `datalake/raw/xlsx/` : hasil pengolahan file Excel lokal
