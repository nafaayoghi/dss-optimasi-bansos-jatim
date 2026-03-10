<div align="center">

# DSS Optimasi Alokasi Bantuan Sosial Jawa Timur

**Decision Support System berbasis web untuk rasionalisasi dan pemerataan kuota bantuan sosial di 38 Kabupaten/Kota Jawa Timur**

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-1.x-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)
![Plotly](https://img.shields.io/badge/Plotly-Interactive-3F4F75?style=flat-square&logo=plotly&logoColor=white)
![Status](https://img.shields.io/badge/Status-Selesai-10B981?style=flat-square)

*Dikembangkan dalam rangka Kerja Praktik di **BPS Kabupaten Bojonegoro** — Desember 2025 s.d. Februari 2026*
*Departemen Matematika, Institut Teknologi Sepuluh Nopember (ITS)*

</div>

---

## Gambaran Umum

Pembagian bantuan sosial di level provinsi sering kali punya satu masalah utama: **ada daerah yang dapat jatah jauh lebih besar dari jumlah orang miskinnya, sementara daerah lain malah kekurangan.** Ketimpangan ini bukan cuma sekadar urusan sisa anggaran, tapi lebih ke soal keadilan distribusi.

Sistem ini dibuat untuk menjawab satu pertanyaan penting: *bagaimana caranya membagikan bansos agar lebih merata dan proporsional, tapi tanpa membuat pemerintah daerah kaget karena jatahnya tiba-tiba berubah drastis?*

Solusinya ada pada pendekatan **Bounded Linear Optimization**. Logikanya sederhana: sistem akan menghitung jatah ideal sesuai porsi kemiskinan tiap daerah, lalu diberikan "rem" (batas toleransi). Dengan begitu, perubahan kuota dari tahun sebelumnya tidak akan terlalu ekstrem, dan angkanya tetap aman untuk dieksekusi oleh pengambil kebijakan.

---

## Fitur Utama

### Dashboard Spasial
- **Peta Choropleth interaktif** — visualisasi kesenjangan (gap) alokasi per Kabupaten/Kota di atas peta OpenStreetMap, dengan highlight daerah terpilih
- **Analisis keadilan distribusi** — leaderboard 5 daerah paling *underfunded* dan *overfunded* berdasarkan hasil optimasi
- **Profil daerah** — ringkasan metrik (penduduk miskin, kuota eksisting, rekomendasi, gap) per Kabupaten/Kota atau agregat seluruh Jatim
- **Grafik komparasi** — kuota eksisting vs target optimasi dalam satu visualisasi overlay

### Perbandingan Multi-Skenario
- Simpan hingga **5 skenario** dengan konfigurasi toleransi dan pagu berbeda ke dalam sesi yang sama
- **Overlay line chart** untuk membandingkan kurva rekomendasi antar skenario secara langsung
- Bar chart **total gap absolut** antar skenario untuk evaluasi dampak kebijakan

### Analisis Efisiensi
- **Scatter plot** proporsi kemiskinan vs kuota per kapita — sebelum dan sesudah optimasi
- Metrik **Koefisien Variasi (CV)** sebagai ukuran pemerataan distribusi
- Hitungan otomatis jumlah daerah *overfunded* dan *underfunded*

### Simulasi & What-If Analysis
- **Simulasi anggaran provinsi** — ubah total pagu untuk melihat dampak realokasi secara langsung
- Slider toleransi kebijakan (0–50%) yang mengontrol batas atas/bawah perubahan kuota tiap daerah
- Ekspor hasil ke **CSV** maupun **Excel (.xlsx)** dengan formatting warna otomatis pada kolom gap

### Halaman Metodologi
- Dokumentasi lengkap algoritma dalam notasi **LaTeX**
- **Simulasi komputasi per daerah** — telusuri setiap langkah kalkulasi untuk Kabupaten/Kota yang dipilih
- **Bounded Knapsack Tracker** — visualisasi bullet chart posisi alokasi final terhadap zona toleransi

---

## Metodologi

Sistem ini bekerja dalam tiga langkah utama:

**1. Alokasi Proporsional Murni**

Setiap daerah mendapatkan jatah ideal berdasarkan proporsi penduduk miskinnya terhadap total Jawa Timur:

$$A_i = w_i \times P$$

di mana $w_i$ adalah rasio penduduk miskin daerah $i$ dan $P$ adalah total pagu provinsi.

**2. Penetapan Batas Toleransi (Shock Prevention)**

Untuk menghindari perubahan kuota yang terlalu drastis, rekomendasi dibatasi dalam koridor aman:

$$L_i \leq R_i \leq U_i$$

$$L_i = E_i \times (1 - \tau), \quad U_i = E_i \times (1 + \tau)$$

di mana $E_i$ adalah kuota eksisting dan $\tau$ adalah toleransi yang dipilih pengguna.

**3. Keputusan Final (Bounded Optimization)**

$$R_i = \begin{cases} L_i & \text{jika } A_i < L_i \\ U_i & \text{jika } A_i > U_i \\ A_i & \text{jika } L_i \leq A_i \leq U_i \end{cases}$$
---

## Struktur Proyek

```
.
├── app.py                              # Aplikasi utama Streamlit
├── bansos_jatim.csv                    # Data alokasi & profil kemiskinan
├── jawa-timur-simplified-topo.json     # GeoJSON batas wilayah Jawa Timur
├── logo.png                            # Logo BPS Kabupaten Bojonegoro
└── .streamlit/
    └── config.toml                     # Konfigurasi tema
```

---

## Instalasi & Menjalankan Aplikasi

**1. Clone repositori**
```bash
git clone https://github.com/username/dss-bansos-jatim.git
cd dss-bansos-jatim
```

**2. Install dependensi**
```bash
pip install streamlit pandas geopandas plotly streamlit-option-menu openpyxl
```

**3. Jalankan aplikasi**
```bash
streamlit run app.py
```

Aplikasi akan terbuka di `http://localhost:8501`.

---

## Sumber Data

| # | Data | Sumber |
|---|------|--------|
| 1 | Data alokasi eksisting bantuan sosial | Portal *SAPA BANSOS* Provinsi Jawa Timur (diakses 25 Desember 2025) |
| 2 | Profil kemiskinan per Kabupaten/Kota | Publikasi resmi BPS: *"Profil Kemiskinan di Kabupaten Bojonegoro Maret 2025"* dan data rujukan kabupaten/kota se-Jawa Timur |

---

## Disclaimer

> Dashboard ini merupakan proyek simulasi akademis dalam rangka Kerja Praktik. Data yang digunakan adalah data publik, dan seluruh hasil rekomendasi sistem **tidak merepresentasikan kebijakan resmi, opini, maupun keputusan aktual** dari BPS maupun Pemerintah Provinsi Jawa Timur.
