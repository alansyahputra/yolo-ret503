# Praktik YOLO RET503 — Transfer Learning dan Perbandingan Model

**Nama:** Alan Syahputra  
**NIM:** 4222401017  
**Mata kuliah:** RET503

## Deskripsi

Proyek ini merupakan praktik deteksi objek menggunakan YOLO pada subset dataset COCO dengan tiga kelas: `person`, `car`, dan `bottle`. Eksperimen membandingkan model dengan bobot pretrained, model yang dilatih dari bobot awal (*scratch*), dan eksperimen tuning ukuran gambar.

## Dataset

Dataset dibuat dari subset COCO dan dibagi menjadi:

| Split | Jumlah gambar | Jumlah file label |
|---|---:|---:|
| Train | 154 | 154 |
| Validation | 44 | 44 |
| Test | 22 | 22 |
| **Total** | **220** | **220** |

Kelas yang digunakan:
- `person`
- `car`
- `bottle`

Terdapat 20 gambar latar belakang tanpa objek target pada dataset awal. File label kosong untuk gambar seperti ini merupakan hal yang normal.

Konfigurasi dataset YOLO berada di `dataset_v1/data.yaml` pada lingkungan kerja Colab. Dataset gambar tidak disertakan dalam repository ini; jalankan skrip persiapan dataset untuk membuatnya kembali.

## Eksperimen

Konfigurasi dasar yang digunakan:
- Model: `yolo26n`
- Epochs: `30`
- Ukuran gambar dasar: `416`
- Batch size: `16`
- Seed: `0`

| Eksperimen | Konfigurasi | mAP50 (validation) | mAP50-95 (validation) | Precision | Recall | Waktu training |
|---|---|---:|---:|---:|---:|---:|
| E1 — Pretrained | Bobot pretrained, imgsz 416 | 0.358 | 0.178 | 0.417 | 0.349 | 18.6 menit |
| E2 — Scratch | Bobot awal acak, imgsz 416 | 0.006 | 0.002 | 0.676 | 0.041 | 18.7 menit |
| E3 — Tuning | Bobot pretrained, imgsz 640 | **0.457** | **0.261** | **0.635** | **0.406** | 42.3 menit |

### Pemilihan model dan pengujian

Model dipilih berdasarkan nilai **mAP50-95 pada validation**. Berdasarkan hasil tersebut, **E3 — Tuning** terpilih dengan mAP50-95 sebesar **0.261**.

Hasil evaluasi pada data test untuk model terpilih:
- **mAP50:** 0.674
- **mAP50-95:** 0.436

Nilai validation dan test mengukur data yang berbeda, jadi keduanya dilaporkan secara terpisah. Hasil di atas adalah hasil dari satu kali proses eksperimen yang tercatat pada laporan.

## Struktur proyek

Berkas utama yang digunakan:

- `00_ambil_subset_coco.py` — mengambil subset gambar dan anotasi COCO.
- `01_cek_anotasi.py` — memeriksa anotasi dan menghasilkan visualisasi pemeriksaan.
- `02_yolo_ke_coco.py` — konversi anotasi YOLO ke format COCO.
- `03_coco_ke_yolo.py` — konversi anotasi COCO ke format YOLO.
- `05_split_dataset_yolo.py` — membagi dataset menjadi train, validation, dan test serta membuat `data.yaml`.
- `06_latih_bandingkan.py` — menjalankan E1, E2, E3, memilih model berdasarkan validation, lalu mengevaluasi model terpilih pada test.
- `hasil_praktik_4222401017.txt` — laporan ringkasan eksperimen.
- `hasil_praktik_4222401017.csv` — hasil eksperimen dalam format CSV.

Nama berkas hasil mungkin berada di folder kerja Colab, bergantung pada lokasi saat skrip dijalankan.

## Cara menjalankan di Google Colab

### 1. Pasang dependensi

```python
!pip install -q ultralytics
```

### 2. Siapkan dataset

Jalankan skrip pengambilan subset COCO dan pemeriksaan anotasi sesuai argumen skrip:

```bash
python 00_ambil_subset_coco.py --kelas person car bottle --jumlah 200 --out dataset_yolo
python 01_cek_anotasi.py dataset_yolo
python 05_split_dataset_yolo.py dataset_yolo dataset_v1
```

Pastikan `dataset_v1/data.yaml` tersedia sebelum training.

### 3. Jalankan eksperimen

```bash
python 06_latih_bandingkan.py --nim 4222401017 --nama "Alan Syahputra" --epochs 30 --imgsz 416 --batch 16
```

Skrip menjalankan tiga eksperimen. Training disarankan menggunakan GPU yang tersedia di Google Colab. Waktu training bergantung pada perangkat dan kondisi runtime.

## Catatan

- Jangan mengunggah token akses, kredensial, atau informasi pribadi ke repository.
- Dataset, cache, dan bobot model berukuran besar (`.pt`) sebaiknya tidak dimasukkan ke GitHub kecuali memang diminta oleh pengajar.
- Pastikan file laporan dan grafik hasil yang dibutuhkan oleh instruksi tugas sudah masuk ke repository.
