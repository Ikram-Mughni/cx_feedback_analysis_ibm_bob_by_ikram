# IBM CX Feedback Intelligence Suite (Enterprise v3.0)
**Capstone Project - IBM Data Bootcamp**  
**Author / Contributor:** Ikramm (`ikramspace1@gmail.com`)  
**Status:** Production Ready (Standalone Web Suite)

---

## 📌 1. Executive Summary & Latar Belakang Masalah

Dalam ekosistem e-commerce dan layanan digital modern, volume ulasan dan keluhan pelanggan (*Customer Experience / CX feedback*) bertumbuh secara eksponensial melalui berbagai kanal (*omnichannel*: Tokopedia, Shopee, App Store, Google Play, WhatsApp CS, Email Support).

### Tantangan Bisnis:
1. **Analisis Manual Lambat & Bias:** Tim CS tidak mampu membedah ribuan ulasan harian secara *real-time*.
2. **Ulasan Multiaspek (Aspect-Based):** Seringkali ulasan pelanggan berisi sentimen kontras dalam satu kalimat (misalnya: *"Barang bagus dan packing rapi, tapi kurirnya lambat 5 hari dan CS jutek"*). Metode klasifikasi sentimen biner sederhana (positif/negatif tunggal) gagal menangkap kompleksitas ini.
3. **Risiko Churn & Reputasi Brand:** Keterlambatan respons terhadap keluhan kritis (P1) memicu *churn rate* tinggi dan risiko viralitas negatif di media sosial.

### Solusi yang Dikembangkan:
**IBM CX Feedback Intelligence Suite v3.0** adalah platform *Customer Experience Analytics* terpadu dengan kapabilitas:
- **Aspect-Based Sentiment Analysis (ABSA)**: Membedah polaritas per dimensi operasional (*Product Quality, Delivery/Logistics, Service Quality, Mobile App, Billing & Refund*).
- **Executive Analytics Dashboard (Chart.js)**: Visualisasi metrik bisnis utama: NPS (*Net Promoter Score*), CSAT (*Customer Satisfaction*), Churn Risk Matrix, dan distribusi isu.
- **Batch Dataset Hub & CSV Pipeline**: Mampu memproses ratusan feedback secara simultan, menyajikan filter pencarian multikriteria, dan mengunduh dataset yang telah diperkaya (*enriched CSV*).
- **SLA & Incident Workflow Management**: Mengubah ulasan negatif menjadi tiket aksi dengan prioritas P1 s/d P4, estimasi SLA jam, dan *PIC assignment*.
- **Generasi Draf Balasan Otomatis Multi-Tone**: Menyusun balasan resmi (*Formal Enterprise*) maupun santai (*Casual Social Media*) lengkap dengan tombol salin satu-klik.
- **Dukungan Slang & Ejaan Informal Bahasa Indonesia**: Mengenali bahasa gaul (*zonk, bapuk, mantul, gacor, gercep, fast respon, slow respon*) serta polaritas sentimen emoji (😡, 💔, ⭐, 🔥, dll.).

---

## 🏛️ 2. Arsitektur & Modul Sistem

Sistem dirancang secara modular dan beroperasi secara *pure standalone client-side* (dapat langsung dibuka di *browser* modern mana pun tanpa memerlukan instalasi server atau dependensi Python/Node.js tambahan):

```
┌────────────────────────────────────────────────────────────────────────┐
│           IBM CX FEEDBACK INTELLIGENCE SUITE (ENTERPRISE v3.0)         │
├───────────────┬────────────────┬───────────────┬───────────────────────┤
│ 1. DASHBOARD  │ 2. DEEP-DIVE   │ 3. BATCH CSV  │ 4. SLA & INCIDENT     │
│   EXECUTIVE   │    (ABSA)      │   PROCESSING  │    WORKFLOW (KANBAN)  │
├───────────────┴────────────────┴───────────────┴───────────────────────┤
│ 5. NLP LEXICON & RULE ENGINE (180+ Kata, Slang, Emojis, Modifiers)     │
└────────────────────────────────────────────────────────────────────────┘
```

### Rincian Modul:

| Modul | Fungsi Utama | Output / Visualisasi |
|---|---|---|
| **1. Executive Dashboard** | Ringkasan KPI makro untuk pimpinan & manajer CX | Metrik Total Ulasan, NPS, CSAT, High Churn Risk, Donut Chart Sentimen, Bar Chart ABSA, Kategori Keluhan |
| **2. Single Review Deep-Dive** | Analisis granular terhadap ulasan spesifik | Kartu polaritas aspek ABSA, Emosi Dominan, Prioritized Action Plan, Draf Balasan Multi-Tone, Tombol Buat Tiket SLA |
| **3. Batch Dataset & CSV Hub** | Pemrosesan massal dataset pelanggan | Tabel ulasan dengan fitur search, multi-filter, ekspor ke CSV ber-BOM UTF-8, modal inspeksi detail |
| **4. SLA & Incident Board** | Manajemen eskalasi operasional dari detraktor | Papan tiket berprioritas (P1 <1 Jam, P2 <4 Jam, P3 <24 Jam, P4 <72 Jam), penugasan PIC, status Open/Resolved |
| **5. NLP Lexicon & Rules** | Manajemen kamus sentimen & kata gaul | Penjelajah kamus leksikon, form penambahan kata kustom, sandbox pengujian bobot kata *real-time* |

---

## 📐 3. Metodologi & Formula Matematis

### A. Aspect-Based Sentiment Analysis (ABSA)
Teks ulasan $T$ dipotong menjadi himpunan klausa $C = \{c_1, c_2, \dots, c_n\}$ menggunakan pemisah tanda baca dan konjungsi subordinatif kontras (*tapi, namun, padahal, sedangkan, meskipun*).

Untuk setiap aspek operasional $A_k$ (misal: *Delivery, Service, Product*):
$$\text{Score}(A_k) = \frac{\sum_{c_i \in C, \text{keyword}(A_k) \in c_i} \text{Polarity}(c_i)}{\text{Hits}(A_k)}$$

Jika dalam satu ulasan klausa produk bernilai $+3.0$ dan klausa kurir bernilai $-2.5$, sistem melaporkan sentimen per aspek secara independen, mencegah *false positive* atau *false negative* agregat.

### B. Customer Satisfaction Score (CSAT)
$$\text{CSAT} = \left( \frac{\text{Jumlah Ulasan Positif \& Sangat Positif}}{\text{Total Ulasan}} \right) \times 100\%$$

### C. Net Promoter Score (NPS) Proyeksi
$$\text{NPS} = \% \text{Promoters (Skor } \ge 1.8\text{)} - \% \text{Detractors (Skor } \le -0.4\text{)}$$
*Skala NPS berkisar antara $-100$ hingga $+100$.*

### D. Penentuan Prioritas SLA & Risiko Churn
- **P1 - Critical (SLA < 1 Jam):** Skor $\le -2.0$ ATAU ulasan menyangkut isu keuangan/bug pembayaran/penipuan dengan intensitas emosi $\ge 8/10$. Risiko Churn: $>85\%$.
- **P2 - High (SLA < 4 Jam):** Skor $-1.9$ s/d $-0.4$ (keterlambatan pengiriman parah, produk cacat). Risiko Churn: $50-70\%$.
- **P3 - Medium (SLA < 24 Jam):** Skor netral $-0.3$ s/d $+0.5$. Risiko Churn: $20-35\%$.
- **P4 - Low (SLA < 72 Jam):** Skor positif $+0.6$ s/d $+3.0$. Risiko Churn: $<15\%$.

---

## 🚀 4. Panduan Menjalankan & Demo Presentasi

### Cara Menjalankan:
1. Buka file [`cx_feedback_analysis.html`](cx_feedback_analysis.html) langsung menggunakan *web browser* modern (Google Chrome, Microsoft Edge, Mozilla Firefox, Safari).
2. Tidak memerlukan server lokal atau `npm start` / `python server`. Semua skrip berjalan secara mandiri.
3. *Offline Friendly*: Sistem dilengkapi *Canvas/SVG fallback* otomatis jika koneksi internet terputus (sehingga grafik tetap tampil sempurna meskipun Chart.js CDN tidak dapat diakses).

### Skenario Demo untuk Ujian / Presentasi Capstone:
1. **Langkah 1: Tampilkan Executive Dashboard (Tab 1)**
   - Tunjukkan metrik bisnis (Total ulasan 25 ulasan omnichannel, NPS +28, CSAT 72%, High Churn Risk 5 pelanggan).
   - Jelaskan grafik ABSA (*Aspect Breakdown*): perhatikan bagaimana kepuasan CS tinggi (+2.1), namun logistik pengiriman menunjukkan tren negatif (-1.2).
2. **Langkah 2: Uji Coba Deep-Dive ABSA (Tab 2)**
   - Klik chip sampel **"⚡ Campuran (Produk Bagus + CS/Kurir Buruk)"**.
   - Perhatikan bagaimana sistem langsung memisahkan sentimen: Kualitas Produk (+2.8 hijau), Pengiriman (-3.0 merah), dan CS Quality (-2.0 merah).
   - Tunjukkan draf balasan resmi dan santai yang dihasilkan secara dinamis, serta tombol **"Salin Teks"**.
   - Klik tombol **"🎫 Buat Tiket SLA"** untuk memasukkan keluhan langsung ke papan insiden.
3. **Langkah 3: Batch Processing & Export CSV (Tab 3)**
   - Peragakan filter pencarian kata kunci (misal: ketik "bapuk" atau "terlambat").
   - Klik tombol **"📥 Unduh CSV Hasil Analisis"** untuk membuktikan bahwa dataset yang telah diperkaya siap diimpor ke tools lanjutan seperti Power BI, Tableau, atau database SQL.
4. **Langkah 4: SLA Incident Workflow (Tab 4)**
   - Perlihatkan daftar tiket kritis (P1 & P2).
   - Klik tombol **"Selesaikan"** pada salah satu tiket dan perhatikan perubahan status serta pembaruan otomatis metrik KPI.
5. **Langkah 5: Cetak Laporan Eksekutif (Tombol Print / PDF)**
   - Klik tombol **"🖨️ Cetak / PDF"** di pojok kanan atas untuk melihat format laporan formal ramah cetak untuk manajemen eksekutif.

---

## 📊 5. Format & Skema Kolom Dataset (CSV)

Dataset yang diproses dan diunduh memiliki struktur data terstandarisasi:
| Nama Kolom | Tipe Data | Deskripsi |
|---|---|---|
| `ID` | String | Identifier unik ulasan (misal: `REV-2001`) |
| `Tanggal` | Date | Tanggal ulasan masuk |
| `Channel` | String | Sumber kanal ulasan (*Tokopedia, Shopee, Google Play, dll.*) |
| `Nama Pelanggan` | String | Nama pengguna/pelanggan |
| `Teks Ulasan` | Text | Isi teks ulasan mentah pelanggan |
| `Sentimen` | Categorical | *Sangat Positif, Positif, Netral, Negatif, Sangat Negatif* |
| `Skor Sentimen` | Float | Skor ternormalisasi (-3.0 s/d +3.0) |
| `Kategori Utama` | Categorical | Aspek operasional dominan |
| `Risiko Churn` | Categorical | Tingkat risiko perpindahan (*Low, Moderate, High, Critical*) |
| `Prioritas SLA` | Categorical | Level urgensi penanganan (*P1, P2, P3, P4*) |

---

## 👨‍💻 Hak Cipta & Lisensi
Proyek ini dikembangkan sebagai bagian dari tugas Capstone Project IBM Data Bootcamp. Hak cipta milik pengembang.
Dilisensikan di bawah [MIT License](https://opensource.org/licenses/MIT).

