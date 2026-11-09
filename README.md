**Nama :** M.Daffa S.F.P.N  
**Kelas :** RM 24 A  
**NIM :** 24552011326

# Tugas Hands-On 3: Pandas Lanjutan

**Kasus:** 08 Student Performance (EdTech), file `case9_student_performance.csv` (800 baris).
**Tujuan:** membersihkan data siswa, mencari faktor yang berkaitan dengan kelulusan, dan menyiapkan data untuk memprediksi `status_kelulusan` (Lulus / Tidak Lulus) tanpa data leakage.

## Ringkasan temuan
1. Faktor jam belajar per minggu berkaitan dengan kelulusan: tingkat lulus tertinggi pada kelompok >30 jam (99.6%) dan terendah pada kelompok 0-10 jam (3.4%), selisih 96.2 poin. Tindak lanjut: buat program belajar terjadwal atau kelompok belajar bagi siswa pada kelompok 0-10 jam.
2. Faktor persentase kehadiran berkaitan dengan kelulusan: tingkat lulus tertinggi pada kelompok >90% (77.5%) dan terendah pada kelompok <=60% (46.4%), selisih 31.1 poin. Tindak lanjut: pasang peringatan dini kehadiran dan hubungi orang tua siswa pada kelompok <=60%.
3. Kehadiran tinggi tidak menjamin kelulusan bila jam belajar rendah: siswa dengan kehadiran di atas 90% tetapi belajar 0-10 jam per minggu hanya 16.0% yang lulus (n = 25), jauh di bawah rata-rata keseluruhan 61.6%. Tindak lanjut: pantau jam belajar, bukan hanya absensi, saat mengidentifikasi siswa berisiko.

Catatan: temuan menunjukkan hubungan (korelasi), belum tentu sebab-akibat.

## Hasil audit kualitas data

| Ukuran | Sebelum | Sesudah |
|---|---:|---:|
| Jumlah baris | 800 | 800 |
| Duplikat | 0 | 0 |
| Missing value (sel) | 511 | 341 |
| Outlier jam belajar | 0 | 0 |

## Keputusan pembersihan
- **Duplikat:** Tidak ada duplikat `student_id` (0 sebelum dan sesudah), jadi tidak ada baris yang dibuang.
- **Target kosong:** Tidak ada baris tanpa `status_kelulusan`, jadi tidak ada baris yang dibuang.
- **Nilai mapel dan rata-rata yang kosong:** dipulihkan dari baris yang sama (rata-rata dihitung dari lima mapel, satu mapel yang kosong dihitung mundur dari rata-rata). Nilai di luar 0-100 dianggap tidak valid.
- **Missing value lainnya:** sengaja tidak diisi saat pembersihan. Diisi di pipeline setelah split (median untuk angka, modus untuk kategorik) dan hanya di-fit pada data train.
- **Outlier:** Aturan IQR per jenis sekolah tidak menemukan outlier pada jam belajar, dan rentang data wajar, sehingga tidak ada nilai yang dipotong.
- **Tipe data:** kolom kategorik diubah ke `category`, `usia` ke `Int64`, dan dibuat target numerik `is_lulus`.

## Data leakage
Nilai rata-rata terendah kelas Lulus adalah 65.0 dan tertinggi kelas Tidak Lulus adalah 64.8, artinya `status_kelulusan` ditentukan oleh `nilai_rata_rata`. Karena itu `nilai_rata_rata` dan kelima nilai mapel **tidak dipakai sebagai fitur model**. Split dilakukan dengan `stratify=y` sebelum imputasi, scaling, dan encoding.

## Penyesuaian tugas
Time series dan RFM dilewati karena dataset tidak punya kolom tanggal maupun transaksi pelanggan. Analisis difokuskan pada groupby lanjutan, binning, pivot, reshape, dan korelasi.

## Cara menjalankan
1. Buka `notebook.ipynb` di Google Colab atau Jupyter.
2. Upload `case9_student_performance.csv` bila diminta.
3. Pilih Runtime -> Restart session and run all.

## Struktur folder
```
tugas-hands-on-3/
├── README.md
├── notebook.ipynb
├── laporan_kualitas.csv
├── data/
│   └── case9_student_performance.csv
└── output/
    ├── data_bersih.csv
    └── kamus_data.csv
```
