# 📊 Amazon Sales Analytics — Power BI Dashboard & AI Insight Pipeline

Proyek analisis data penjualan e-commerce fashion berbasis data Amazon India-style untuk periode **Maret–Juni 2022**.

Proyek ini menggabungkan **SQL** untuk pengolahan data, **Power BI** untuk visualisasi dan dashboard, serta **Langflow + LLM** untuk menghasilkan insight dan rekomendasi bisnis secara otomatis.

---

## 🎯 Tujuan Proyek

Proyek ini dibuat untuk menunjukkan alur kerja analisis data secara end-to-end, mulai dari data mentah hingga menghasilkan insight bisnis.

**Alur utama proyek:**

1. Mengolah data mentah menggunakan SQL.
2. Menghasilkan dataset agregat untuk kebutuhan analisis.
3. Memvalidasi hasil pengolahan data.
4. Membuat dashboard interaktif menggunakan Power BI.
5. Menghubungkan dataset dengan Langflow dan LLM.
6. Menghasilkan insight dan rekomendasi bisnis secara otomatis.
7. Melakukan validasi kembali terhadap hasil analisis AI.

---

## 📁 Data Mentah

Data awal yang digunakan adalah dataset penjualan e-commerce yang berisi informasi transaksi seperti:

- Kategori produk
- Jumlah barang (`Qty`)
- Nilai transaksi (`Amount`)
- Status pesanan
- Kota
- SKU produk
- Tanggal transaksi

Data mentah kemudian diolah dan diagregasi sesuai kebutuhan analisis.

---

## 🧹 Data Processing

Pengolahan data dilakukan menggunakan **SQL** untuk menghasilkan beberapa ringkasan yang lebih mudah digunakan oleh Power BI dan pipeline AI.

Contoh query agregasi kategori:

```sql
SELECT 
    Category AS kategori,
    SUM(Qty) AS total_terjual,
    SUM(Amount) AS total_omset
FROM Amazon_Sale_Report
WHERE Status = 'Shipped'
GROUP BY Category
ORDER BY total_omset DESC;
```

Query tersebut digunakan untuk mendapatkan total unit terjual dan total omset berdasarkan kategori produk dengan mempertimbangkan transaksi berstatus *Shipped*.

---

## 📂 Dataset Hasil Agregasi

Dari proses pengolahan data, dihasilkan lima dataset utama:

| File | Isi |
| :--- | :--- |
| `1_kategori_penjualan.csv` | Total unit terjual dan omset berdasarkan kategori |
| `2_top_kota.csv` | Jumlah pesanan dan nilai transaksi berdasarkan kota |
| `3_status_pesanan.csv` | Distribusi pesanan berdasarkan status dan kategori |
| `4_top_produk_sku.csv` | 10 SKU dengan penjualan tertinggi |
| `5_tren_harian.csv` | Omset dan jumlah pesanan berdasarkan tanggal |

### 📌 Ringkasan Angka Kunci
- **Total transaksi:** 128.975
- **Unit terjual pada transaksi Shipped:** 78.009 unit
- **Transaksi Shipped:** 77.804
- **Transaksi Cancelled:** 18.332
- **Total omset transaksi Shipped:** ₹50.324.255
- **Kategori dengan omset terbesar:** Set
- **Kategori dengan unit terjual terbesar:** Kurta
- **Kota dengan transaksi Shipped terbanyak:** Bengaluru
- **Kontribusi Omset:** Set dan Kurta menyumbang sekitar 78,8% dari total omset.

> **Catatan:** Jumlah unit terjual (`Qty`) dan jumlah transaksi/order merupakan dua metrik yang berbeda. Oleh karena itu, **78.009** merupakan jumlah unit, sedangkan **77.804** merupakan jumlah transaksi *Shipped*.

---

## 📈 Visualisasi — Power BI

Dataset hasil agregasi digunakan sebagai sumber data untuk membangun dashboard Power BI.

**Dashboard mencakup:**
- Total transaksi
- Total transaksi *Shipped*
- Total transaksi *Cancelled*
- Total omset
- Tren omset harian
- Omset berdasarkan kategori
- Distribusi transaksi berdasarkan kota
- Distribusi status pesanan berdasarkan kategori
- Top 10 SKU berdasarkan penjualan

### Contoh Dashboard
<img width="1522" height="830" alt="image" src="https://github.com/user-attachments/assets/109f94f7-83ef-44eb-a5b7-e41cca1c638d" />



---

## 🔍 Data Validation & QA

Validasi dilakukan untuk memastikan angka yang ditampilkan pada dashboard sesuai dengan data sumber.

Selama proses QA, ditemukan ketidaksesuaian pada nilai pendapatan di salah satu visual Power BI. Beberapa nilai terbaca **10× lebih besar** dibandingkan nilai pada data sumber.

**Masalah tersebut ditelusuri dengan membandingkan:**
1. Data CSV hasil agregasi
2. Hasil query SQL
3. Nilai pada Power BI
4. Hasil visual dashboard

Setelah proses pengecekan dan perbaikan, total omset pada dashboard disesuaikan dengan hasil agregasi data sumber, yaitu: **₹50.324.255**.

Proses *cross-check* ini dilakukan sebelum dashboard digunakan untuk analisis lebih lanjut.

---

## 🤖 AI Insight Pipeline — Langflow

Setelah dataset selesai diproses, lima file agregasi digunakan sebagai input pada pipeline AI menggunakan Langflow.

### Arsitektur Flow

```text
5 CSV
 │
 ├── CSV Loader ×5
 │
 ↓
Combine / Parser
 │
 ↓
Prompt Template
 │
 ↓
LLM
 │
 ↓
Chat Output
```

Pipeline ini dirancang untuk menghasilkan narasi analisis dan rekomendasi bisnis berdasarkan data yang tersedia.

### Node Flow
1. **CSV Loader ×5:** Memuat lima dataset hasil agregasi.
2. **Combine / Parser:** Menggabungkan data menjadi konteks terstruktur untuk LLM.
3. **Prompt Template:** Memberikan instruksi analisis kepada LLM.
4. **LLM Node:** Memproses data dan menghasilkan insight serta rekomendasi.
5. **Chat Output:** Menampilkan hasil analisis dalam bentuk laporan.

---

## 🧠 Prompt Template

Prompt yang digunakan pada pipeline:

```text
Kamu adalah data analyst e-commerce fashion.

Berikut adalah data penjualan periode Maret–Juni 2022:

{data_kategori}
{data_kota}
{data_status_pesanan}
{data_sku_terlaris}
{data_tren_harian}

Tugas:
1. Identifikasi 3 insight utama dari data.
2. Analisis pola berdasarkan kategori, wilayah, atau waktu.
3. Identifikasi potensi masalah berdasarkan data yang tersedia.
4. Berikan 3 rekomendasi bisnis yang actionable dan didukung oleh data.
5. Tulis hasil analisis dalam bahasa natural seperti laporan analis kepada manajemen.

Aturan:
- Gunakan hanya angka dan informasi yang tersedia dalam data.
- Jangan mengarang angka yang tidak terdapat dalam dataset.
- Jangan menyimpulkan penyebab jika penyebab tersebut tidak dapat dibuktikan dari data.
- Jika diperlukan data tambahan untuk mendukung suatu kesimpulan, nyatakan bahwa data tersebut belum tersedia.
- Bedakan antara fakta dari data dan interpretasi.
```

---

## 📊 Contoh Hasil AI Insight

Pipeline menghasilkan laporan analisis yang membahas beberapa aspek seperti:
- Performa kategori produk
- Distribusi penjualan berdasarkan wilayah
- Tren penjualan berdasarkan waktu
- Status pesanan
- Rekomendasi berdasarkan pola yang ditemukan pada data

Hasil dari LLM tetap perlu divalidasi kembali terhadap dataset sebelum digunakan sebagai kesimpulan akhir.

---

## 🔎 Validasi Output AI

Output dari LLM tidak digunakan secara langsung tanpa pengecekan.

Setiap insight dibandingkan kembali dengan dataset untuk memastikan:
- Angka yang disebutkan sesuai dengan data.
- Definisi metrik tidak berubah.
- Tidak terdapat asumsi yang tidak didukung data.
- Rekomendasi memiliki hubungan dengan hasil analisis.

Sebagai contoh, perbedaan antara jumlah transaksi dan transaksi *Shipped* tidak secara otomatis menunjukkan adanya pesanan yang hilang, masalah stok, atau masalah logistik. Untuk menentukan penyebabnya diperlukan data tambahan seperti alasan pembatalan, retur, stok, atau informasi logistik.

Hal ini menunjukkan bahwa LLM digunakan sebagai alat bantu analisis, bukan sebagai pengganti proses validasi data.

---

## 🛠️ Tools yang Digunakan

| Tools | Penggunaan |
| :--- | :--- |
| **SQL** | Pengolahan dan agregasi data |
| **Power BI Desktop** | Data modeling dan visualisasi dashboard |
| **Langflow** | Orkestrasi pipeline AI |
| **LLM (Claude/GPT/Gemini)** | Generasi insight dan rekomendasi |
| **GitHub** | Dokumentasi dan version control |

---

## 🚀 Pengembangan Lanjutan

Beberapa pengembangan yang dapat dilakukan pada proyek ini:
- Menambahkan *conditional/router node* untuk memberikan analisis khusus berdasarkan kondisi tertentu.
- Menambahkan analisis *cancel rate* dan *revenue contribution* secara otomatis.
- Menambahkan RAG/*vector store* untuk memungkinkan sesi tanya-jawab lanjutan berdasarkan dataset.
- Menghubungkan pipeline dengan dataset yang diperbarui secara berkala.
- Menambahkan metrik seperti *Average Order Value* (AOV) apabila struktur data memungkinkan.

---

## 📝 Kesimpulan

Proyek ini menunjukkan alur analisis data dari **data mentah → data processing → validasi → visualisasi → AI insight**.

Power BI digunakan untuk menyajikan data dalam bentuk dashboard interaktif, sementara Langflow dan LLM digunakan untuk membantu menghasilkan narasi insight dan rekomendasi bisnis.

Proses validasi dilakukan pada tahap pengolahan data, dashboard, maupun output AI untuk memastikan hasil analisis tetap sesuai dengan data yang tersedia.

Dengan demikian, proyek ini tidak hanya berfokus pada pembuatan visualisasi, tetapi juga menunjukkan pentingnya *data validation*, *critical thinking*, dan penggunaan AI secara terkontrol dalam proses analisis data.
