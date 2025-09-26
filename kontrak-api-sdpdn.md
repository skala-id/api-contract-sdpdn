# Kontrak API — Data Statistik Sektoral Daerah

> Dokumen ini mendeskripsikan kontrak API Satu Data Pemerintahan Dalam Negeri berupa data statistik.
> - Nama layanan: Data Statistik Sektoral API dari Pemda ke Portal Pelita
> - Versi kontrak: 1.3
> - Tanggal Update: 26 September 2025
> - Base URL: https://api.contoh.go.id (URL Portal Pemda - bisa menggunakan SPLP)
> - Auth: Bearer Token (opsional untuk auth, kecuali data BNBA harus menggunakan token)

## Header umum

- Accept: application/json
- Authorization: Bearer {{token-opsional}}

## Paginasi (query standar)

- page: integer, min 1, default 1
- page_size: integer, default 25 // tidak perlu di query param
- tahun: integer, default 2024

## Terdapat dua API untuk diintegrasikan:

1. List Dataset
2. Detail Dataset

## Endpoint List Dataset

### GET /v1/dataset

- Fungsi: Mengembalikan daftar dataset beserta definisi field dan baris data.
- Query parameter opsional:
    - page
    - tahun: integer // tahun data berdasarkan daftar data daerah, default tahun 2024
    - kode_provinsi: string // catatan untuk integrasi provinsi
    - kode_kab_kota: string // optional
    - kode_bidang_urusan: string

### Respons JSON List Dataset
```json
{
  "message": "Berhasil",
  "code": 200,
  "metadata": {
    "total_data": 1000,
    "total_page": 50,
    "page": 1,
    "page_size": 25
  },
  "datalist": [
    {
      "id": "fdc985f7-2a29-4b75-a08c-c2cc9765cbc2",
      "tahun": 2025,
      "kode_provinsi": "11",
      "kode_kab_kota": "11.01",
      "kode_urusan": "1",
      "nama_urusan": "URUSAN PEMERINTAHAN WAJIB YANG BERKAITAN DENGAN PELAYANAN DASAR",
      "kode_bidang_urusan": "1.06",
      "nama_bidang_urusan": "URUSAN PEMERINTAHAN BIDANG SOSIAL",
      "nama_data": "Jumlah Penerima Bantuan Langsung Pangan Pemerintah Provinsi Gorontalo (BLP3G)",
      "nama_indikator": "Jumlah Penerima Bantuan Langsung Pangan Pemerintah Provinsi Gorontalo (BLP3G)",
      "sumber_referensi": "",
      "kode_produsen_data": "", // kode skpd atau kode opd
      "produsen_data": "Dinas Sosial",
      "jadwal_rilis": "2025-01-17", // BELUM SEPAKAT -perlu dipindah di detail nilai data berdasarkan di NTT
      "jadwal_pemutakhiran": "Tahunan",
      "jenis_data": "statistik",
      "kode_standard_data": "", // dari portal indah bps - Kode SDS
      "klasifikasi_data": "terbuka",
      "definisi": "", // dari portal indah bps - Definisi
      "klasifikasi_penyajian": "", // dari portal indah bps - Klasifikasi Penyajian
      "satuan": "Orang",
      "kategori_rad": "", // berdasarkan Rancangan Arsitektur Data dan Informasi SPBE
      "status_prioritas": false,
      "tanggal_data": "2025-07-17",
      "endpoint": "https://data.contoh.go.id/v1/dataset/{{uuid}}",
    }
  ]
}
```

### Respons kesalahan
- 400 Bad Request
```json
{ "message": "Parameter tidak valid", "code": 400, "errors": ["page harus >= 1"] }
```

- 401 Unauthorized
```json
{ "message": "Token tidak valid atau kadaluwarsa", "code": 401 }
```

- 404 Not Found
```json
{ "message": "Dataset tidak ditemukan", "code": 404 }
```

- 429 Too Many Requests
```json
{ "message": "Terlalu banyak permintaan, coba lagi nanti", "code": 429 }
```

- 500 Internal Server Error
```json
{ "message": "Terjadi kesalahan di server", "code": 500 }
```

- 502 Bad Gateway
```json
{ "message": "Server Tidak Aktif", "code": 502}
```

### Keterangan Variabel

1. ID: Identifier unik untuk setiap dataset - menggunakan uuid
2. Tahun: Tahun data berdasarkan daftar data
3. Kode Provinsi: Kode wilayah provinsi
4. Kode Kabupaten/Kota: Kode wilayah kabupaten atau kota
5. Kode Urusan: Kode urusan pemerintahan
6. Nama Urusan: Nama urusan pemerintahan
7. Kode Bidang Urusan: Kode bidang urusan pemerintahan
8. Nama Bidang Urusan: Nama bidang urusan pemerintahan
9. Nama Data: Nama dari indikator atau variable yang masuk dalam dalam daftar data
10. Nama Indikator: Nomenklatur dari indikator pembangunan sesuai dengan kode dan sumber referensi yang digunakan
11. Sumber Referensi: Nama dokumen perencanaan Pembangunan atau regulasi yang menjadi basis penentuan daftar data
12. Kode Produsen Data: Kode SKPD atau kode OPD
13. Produsen Data: Organisasi Perangkat Daerah yang menjadi produsen data
14. Jadwal Rilis: Tanggal rilis data
15. Jadwal Pemutakhiran: Jadwal/periode data dikumpulkan/dipublikasikan
16. Jenis Data: Jenis data dikategorisasikan atau diklasifikasikan menjadi dua kategori yaitu Statistik dan Geospasial
17. Kode Standar Data: Kode standar data statistik (SDS) pada INDAH (https://indah.bps.go.id) atau kode SPD/kode unsur pada KUGI (https://kugi.ina-sdi.or.id). Bagian ini dapat diisi dengan "N/A" jika Data belum ada standarnya
18. Klasifikasi Data: Klasifikasi yang dibagi menjadi 3 kategori hak akses yaitu terbuka, tertutup, dan terbatas
19. Definisi: Penjelasan tentang data yang memberi batas atau membedakan secara jelas arti dan cakupan data tertentu dengan data yang lain (bagian ini WAJIB diisi apabila poin 17 kosong atau N/A)
20. Klasifikasi Penyajian: Klasifikasi yang digunakan untuk data numerik, misal: penyajian data menurut kabupaten/kota, desa/kelurahan, jenis kelamin atau kategori tertentu
21. Satuan: Besaran tertentu dalam Data yang digunakan sebagai standar untuk mengukur atau menakar sebagai sebuah keseluruhan (bagian ini wajib diisi apabila poin 17 kosong atau N/A)
22. Kategori RAD: Kategori berdasarkan Rancangan Arsitektur Data dan Informasi SPBE (RAD) yang dapat diakses pada tautan https://sisae.spbe.go.id/index.php/_RAD
23. Status Prioritas: Klasifikasi data yang dibagi menjadi dua kategori yaitu data prioritas dan non-prioritas
24. Tanggal Data: Tanggal data aktual
25. Endpoint: URL untuk mengakses detail dataset

## Endpoint Detail Dataset

### GET /v1/dataset/{{uuid}}

- Fungsi: Mengembalikan detail data beserta valuenya.
- Query parameter opsional:
    - page
    - tahun // semua tahun ditampilkan jika tidak difilter

### Respons JSON Detail Dataset
```json
{
  "message": "Berhasil",
  "code": 200,
  "metadata": {
    "total_data": 1000,
    "total_page": 50,
    "page": 1,
    "page_size": 25
  },
  "field": [
    {
      "name_field": "variabel_1",
      "data_type": "string",
      "length": 255,
      "klasifikasi_data": "terbuka"
    },
    {
      "name_field": "variabel_2",
      "data_type": "string",
      "length": 50,
      "klasifikasi_data": "terbuka"
    },
    {
      "name_field": "nik",
      "data_type": "string",
      "length": 16,
      "klasifikasi_data": "tertutup"
    },
    {
      "name_field": "tahun",
      "data_type": "integer",
      "length": 4,
      "klasifikasi_data": "terbuka"
    },
    {
      "name_field": "waktu_rilis",
      "data_type": "date",
      "length": 30,
      "klasifikasi_data": "terbuka"
    },
    {
      "name_field": "variabel_3",
      "data_type": "string",
      "length": 255,
      "klasifikasi_data": "terbuka"
    }
  ],
  "data": [
    {
      "variabel_1": "Nilai 1",
      "variabel_2": "Nilai 2",
      "nik": "5556897432891234",
      "tahun": 2025,
      "waktu_rilis": "2025-09-19",
      "variabel_3": "Nilai 3"
    },
    {
      "variabel_1": "Nilai 4",
      "variabel_2": "Nilai 5",
      "nik": "5556897432891235",
      "tahun": 2025,
      "waktu_rilis": "2025-09-19",
      "variabel_3": "Nilai 6"
    },
    {
      "variabel_1": "Nilai 7",
      "variabel_2": "Nilai 8",
      "nik": "5556897432891236",
      "tahun": 2025,
      "waktu_rilis": "2025-09-19",
      "variabel_3": "Nilai 9"
    }
  ]
}
```

### Respons kesalahan
- 400 Bad Request
```json
{ "message": "Parameter tidak valid", "code": 400, "errors": ["page harus >= 1"] }
```

- 401 Unauthorized
```json
{ "message": "Token tidak valid atau kadaluwarsa", "code": 401 }
```

- 404 Not Found
```json
{ "message": "Dataset tidak ditemukan", "code": 404 }
```

- 429 Too Many Requests
```json
{ "message": "Terlalu banyak permintaan, coba lagi nanti", "code": 429 }
```

- 500 Internal Server Error
```json
{ "message": "Terjadi kesalahan di server", "code": 500 }
```

- 502 Bad Gateway
```json
{ "message": "Server Tidak Aktif", "code": 502}
```

## Catatan:

Data Tahun perlu pembahasan lebih detail, untuk mengakomodir periode data, tahun data, jadwal rilis dll

