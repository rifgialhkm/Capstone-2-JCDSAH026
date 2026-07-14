# Capstone Project Module 2 — Product & Pricing Strategy Analysis: "Kuota Ketengan (App-Specific)"

---

## 1. Background

Telkomsel menawarkan 5 kategori paket data dengan struktur harga yang bervariasi, salah satunya adalah **"Kuota Ketengan (App-Specific)"** — paket khusus untuk aplikasi tertentu (YouTube & TikTok), dengan harga Rp5.000–6.000, menjadikannya kategori termurah dari seluruh lini produk data Telkomsel.

Eksplorasi awal terhadap data transaksi menunjukkan temuan menarik: **price-per-MB** kategori ini (~Rp5,34/MB) jauh di bawah kategori lain (Device Bundling ~Rp218,96/MB, Internet OMG ~Rp131,28/MB) — padahal rata-rata payload yang terpakai per sesi hampir sama di seluruh kategori (~1.024–1.030 MB/sesi). Artinya, pelanggan mengonsumsi data dalam jumlah yang sebanding dengan kategori lain, namun dikenakan tarif jauh lebih murah.

Temuan ini memunculkan pertanyaan bisnis penting dari sudut **product & pricing strategy**: apakah paket app-specific murah ini adalah strategi akuisisi pelanggan yang disengaja dan sehat, ataukah berpotensi menjadi *revenue cannibalization* yang merugikan perusahaan.

### Metodologi & Batasan Data

- **Price-per-MB** dihitung sebagai `price paket / rata-rata payload per sesi`, digunakan sebagai **proxy directional** untuk membandingkan efisiensi monetisasi antar kategori — bukan unit economics sebenarnya, karena dataset tidak memuat jumlah sesi per siklus pembelian paket maupun periode validitas kuota.
- Dataset **tidak memiliki breakdown traffic per aplikasi** (mis. berapa MB benar-benar dipakai untuk YouTube vs traffic lain), sehingga analisis "kewajaran konsumsi" hanya bisa membandingkan total payload per sesi antar kategori, bukan memverifikasi kesesuaian traffic dengan tujuan produk (app-specific).
- Analisis cost-to-serve berdasarkan `network_type` bersifat **estimasi kualitatif** (proporsi pemakaian jaringan sebagai proxy risiko biaya), bukan perhitungan biaya infrastruktur aktual karena data biaya tidak tersedia.
- **[Update pasca-EDA]** Uji Kruskal-Wallis mengonfirmasi `payload_mb` per sesi berdistribusi identik (menyerupai uniform ~10–2.048 MB) di seluruh `package_category` (p=0,876, H=1,21) — mengindikasikan payload dibangkitkan independen dari jenis paket, bukan hasil simulasi perilaku konsumsi realistis. **Implikasi:** Masalah Turunan #1 (kewajaran pola konsumsi app-specific) tidak dapat divalidasi secara perilaku dari dataset ini dan direframe sebagai keterbatasan; fokus case dialihkan sepenuhnya ke struktur pricing & efisiensi monetisasi (Masalah Turunan #3 & #4), yang tetap valid karena tidak bergantung pada realisme payload.

---

## 2. Problem Statement

**Masalah Utama:** Ketidakjelasan efektivitas strategi monetisasi paket "Kuota Ketengan (App-Specific)" — apakah kategori ini berfungsi sebagai produk akuisisi pelanggan yang sehat, ataukah menimbulkan risiko *revenue leakage* melalui pola konsumsi yang jauh melebihi wajar untuk kategori app-specific, atau kanibalisasi terhadap paket lain yang lebih menguntungkan.

**Masalah Turunan:**
1. Bagaimana pola konsumsi (payload per sesi) pengguna Kuota Ketengan dibanding kategori lain — apakah sebanding/konsisten, atau ada indikasi volume konsumsi yang tidak wajar untuk kategori app-specific?
2. Apakah pengguna Kuota Ketengan bersifat eksklusif (hanya pakai kategori ini), atau juga menggunakan kategori paket lain — sebagai indikator potensi *cross-sell/upsell*?
3. Berapa selisih efisiensi monetisasi (price-per-MB, sesuai definisi & batasan metodologi di atas) antara Kuota Ketengan dan kategori lain, dan berapa estimasi *opportunity cost* jika sebagian pola pemakaiannya setara paket lain yang lebih mahal?
4. Apakah ada pola jenis jaringan (`network_type`) tertentu — misal 5G — yang dipakai tidak proporsional oleh segmen paket termurah ini (potensi cost-to-serve mismatch, bersifat estimasi kualitatif)?

---

## 3. Goals

- **Revenue Optimization:** Menentukan apakah perlu penyesuaian harga/kuota pada kategori Kuota Ketengan berdasarkan pola konsumsi aktual.
- **Cross-sell Opportunity:** Mengidentifikasi apakah pengguna Kuota Ketengan adalah target potensial untuk *upgrade* ke paket lebih mahal.
- **Cost-to-Serve Alignment (Estimasi Kualitatif):** Menilai apakah proporsi jenis jaringan yang dipakai sepadan dengan harga yang dibayar pelanggan pada kategori ini.
