# CBR Putusan HAM — Case-Based Reasoning untuk Putusan Pelanggaran HAM

Proyek ini membangun sistem **Case-Based Reasoning (CBR)** untuk menganalisis dan mengambil kasus putusan pengadilan terkait **Hak Asasi Manusia (HAM)** dari Direktori Mahkamah Agung RI. Pipeline terdiri dari 5 tahap yang dijalankan secara berurutan di **Google Colab**.

---

## Struktur Folder di Google Drive

```
MyDrive/
└── Penalaran Komputer (460, 464)/
    ├── Data/
    │   ├── HAM/                  ← PDF putusan (input manual, isi sendiri)
    │   ├── raw/                  ← Output Tahap 1: file case_001.txt dst.
    │   ├── case_metadata.csv     ← Output Tahap 2: metadata + full_text
    │   ├── eval/
    │   │   ├── queries.json      ← Query uji (dibuat di Tahap 3)
    │   │   ├── top_tfidf_terms.png
    │   │   ├── error_analysis.csv
    │   │   ├── retrieval_metrics.csv
    │   │   └── ...
    │   └── results/
    │       ├── svm_predictions.csv
    │       ├── nb_predictions.csv
    │       ├── predictions.csv
    │       ├── model_comparison.csv
    │       └── prediction_metrics.csv
    └── logs/
        ├── cleaning.log
        ├── representation.log
        ├── retrieval.log
        ├── predict.log
        └── evaluation.log
```

---

## Prasyarat

- Akun **Google** dengan akses **Google Colab** dan **Google Drive**
- **50 file PDF** putusan HAM dari [Direktori MA RI](https://putusan3.mahkamahagung.go.id), diunggah ke folder `MyDrive/Penalaran Komputer (460, 464)/Data/HAM/`
- Semua library diinstall otomatis via `%pip install` di awal setiap notebook

---

## Cara Menjalankan Pipeline (End-to-End)

> Jalankan notebook **secara berurutan**. Setiap notebook menghasilkan output yang digunakan oleh notebook berikutnya.

### Tahap 1 — Scraping & Cleaning (`01_scraper.ipynb`)

**Tujuan:** Konversi PDF → teks bersih, simpan ke `/Data/raw/`

1. Buka `01_scraper.ipynb` di Google Colab
2. Pastikan 50 PDF sudah ada di `MyDrive/.../Data/HAM/`
3. Jalankan semua cell dari atas ke bawah
4. **Output:** `Data/raw/case_001.txt` ... `case_050.txt`

**Library yang diinstall:**
```
pdfminer.six
```

---

### Tahap 2 — Representasi Kasus (`02_representation.ipynb`)

**Tujuan:** Ekstrak metadata dari teks dan simpan ke `case_metadata.csv`

1. Buka `02_representation.ipynb` di Google Colab
2. Pastikan Tahap 1 sudah selesai (folder `raw/` terisi)
3. Jalankan semua cell dari atas ke bawah
4. **Output:** `Data/case_metadata.csv` dengan kolom:
   - `case_id`, `nomor_perkara`, `tahun_putusan`, `bulan_putusan`, `tanggal_putusan`
   - `jenis_perkara`, `tingkat_pemeriksaan`, `lembaga_peradilan`
   - `pasal`, `hakim_ketua`, `ringkasan_fakta`, `jumlah_kata_putusan`, `full_text`

**Library yang diinstall:**
```
pandas
```

---

### Tahap 3 — Case Retrieval (`03_retrieval.ipynb`)

**Tujuan:** Bangun model TF-IDF + SVM untuk retrieval kasus serupa

1. Buka `03_retrieval.ipynb` di Google Colab
2. Pastikan `case_metadata.csv` sudah ada
3. Jalankan semua cell dari atas ke bawah
4. **Output:**
   - `Data/eval/queries.json` — query uji dengan ground truth
   - `Data/eval/top_tfidf_terms.png` — visualisasi top term TF-IDF
   - `Data/eval/error_analysis.csv` — analisis kasus yang salah prediksi

**Library yang diinstall:**
```
scikit-learn, pandas, numpy, matplotlib, seaborn
```

---

### Tahap 4 — Prediksi Solusi (`04_predict.ipynb`)

**Tujuan:** Prediksi amar putusan untuk kasus baru menggunakan CBR

1. Buka `04_predict.ipynb` di Google Colab
2. Pastikan Tahap 3 sudah selesai (`queries.json` sudah ada)
3. Jalankan semua cell dari atas ke bawah
4. **Output:**
   - `Data/results/svm_predictions.csv`
   - `Data/results/nb_predictions.csv`
   - `Data/results/predictions.csv`
   - `Data/results/prediction_metrics.csv`

**Library yang diinstall:**
```
scikit-learn, pandas, numpy
```

---

### Tahap 5 — Evaluasi (`05_evaluation.ipynb`)

**Tujuan:** Evaluasi menyeluruh performa retrieval dan prediksi (SVM vs Naive Bayes)

1. Buka `05_evaluation.ipynb` di Google Colab
2. Pastikan semua tahap sebelumnya sudah selesai
3. Jalankan semua cell dari atas ke bawah
4. **Output:**
   - `Data/eval/retrieval_metrics.csv`
   - `Data/results/model_comparison.csv`
   - `Data/results/prediction_metrics.csv`
   - `Data/eval/evaluation_summary.png`
   - `Data/eval/svm_lime_errors.json`
   - `Data/eval/naive_bayes_lime_errors.json`

**Library yang diinstall:**
```
lime, nltk, scikit-learn, pandas, numpy, matplotlib, seaborn
```

---

## Ringkasan Library (Semua Tahap)

| Library | Digunakan di |
|---|---|
| `pdfminer.six` | Tahap 1 |
| `pandas` | Tahap 2, 3, 4, 5 |
| `scikit-learn` | Tahap 3, 4, 5 |
| `numpy` | Tahap 3, 4, 5 |
| `matplotlib` | Tahap 3, 5 |
| `seaborn` | Tahap 3, 5 |
| `lime` | Tahap 5 |
| `nltk` | Tahap 5 |

---

## Catatan Penting

- Semua notebook menggunakan **Google Colab + Google Drive** — tidak perlu install apapun di lokal
- Folder `raw/`, `eval/`, `results/`, dan `logs/` dibuat **otomatis** saat notebook dijalankan
- Satu-satunya folder yang perlu dibuat manual adalah `HAM/` beserta isi PDF-nya
- Jalankan notebook **sesuai urutan nomor** (01 → 02 → 03 → 04 → 05)
