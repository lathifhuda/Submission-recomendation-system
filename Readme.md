# Laporan Proyek Machine Learning - Lathif Nurma Huda

## Domain Proyek

Industri game digital terus berkembang pesat, menawarkan jutaan judul game di berbagai platform seperti Steam. Dengan volume game yang sangat besar ini, pemain seringkali kesulitan untuk menemukan game baru yang benar-benar sesuai dengan minat dan preferensi mereka. Tantangan dalam penemuan konten ini tidak hanya memengaruhi pengalaman pengguna tetapi juga menghambat visibilitas game, terutama bagi pengembang independen. Sistem rekomendasi hadir sebagai solusi untuk mengatasi masalah ini dengan mempersonalisasi pengalaman pengguna dan membantu mereka menemukan game yang relevan di tengah lautan pilihan.

Proyek ini bertujuan untuk mengembangkan sistem rekomendasi game berbasis konten menggunakan dataset dari platform distribusi game digital terkemuka, Steam. Dengan menganalisis karakteristik (genre, kategori, deskripsi) dari game, sistem ini akan mampu menyarankan game lain yang memiliki konten serupa dengan game yang disukai pengguna. Penyelesaian masalah ini penting untuk meningkatkan kepuasan pemain, mendorong eksplorasi katalog game, dan mendukung ekosistem pengembang game dengan meningkatkan jangkauan audiens.

*   **Hasil Riset Terkait / Referensi:**
    *   [Masukkan referensi artikel ilmiah atau laporan industri yang relevan di sini. Contoh format:]
        *   [1] Smith, J. (Tahun). *Pentingnya Sistem Rekomendasi dalam Industri Game Digital.* Jurnal Industri Game, Vol(Issue), pp-pp.
        *   [2] Jones, A., & Brown, B. (Tahun). *Content-Based Filtering for Game Recommendations.* Prosiding Konferensi Internasional tentang Sistem Rekomendasi, pp-pp.
        *   [Masukkan referensi lain yang relevan]

## Business Understanding

Bagian ini menguraikan masalah yang dihadapi dalam domain game digital dan tujuan yang ingin dicapai melalui pembangunan sistem rekomendasi.

### Problem Statements

1.  Pemain kesulitan menavigasi katalog game yang luas di platform digital seperti Steam untuk menemukan game baru yang sesuai dengan preferensi mereka.
2.  Platform distribusi game kehilangan potensi pendapatan karena pengguna mungkin tidak menyadari game yang relevan dengan minat mereka.
3.  Pengembang game menghadapi tantangan dalam meningkatkan visibilitas game mereka kepada target audiens yang tepat.

### Goals

1.  Membangun sistem rekomendasi yang dapat mengidentifikasi dan menyarankan game yang relevan berdasarkan karakteristik game yang disukai pengguna.
2.  Meningkatkan kemungkinan pengguna menemukan game yang menarik bagi mereka, berpotensi meningkatkan keterlibatan pengguna dan penjualan.
3.  Menyediakan mekanisme bagi game untuk direkomendasikan kepada pengguna yang paling mungkin tertarik, membantu meningkatkan visibilitas game.

### Solution Statements

1.  Mengembangkan sistem rekomendasi berbasis konten menggunakan representasi teks game (genre, kategori) yang diekstraksi menggunakan TF-IDF dan mengukur kemiripan antar game menggunakan Cosine Similarity.
2.  Melakukan tuning pada parameter TF-IDF (misalnya, `ngram_range`, `max_df`, `min_df`) untuk mengoptimalkan representasi tekstual game dan berpotensi meningkatkan kualitas rekomendasi.
3.  Mengevaluasi kinerja sistem rekomendasi menggunakan metrik Precision@k, Recall@k, dan NDCG@k untuk mengukur seberapa baik sistem merekomendasikan game yang relevan.

## Data Understanding

Dataset yang digunakan dalam proyek ini adalah "Steam Store Games" yang diunduh dari Kaggle. Dataset ini berisi informasi komprehensif tentang game yang tersedia di platform Steam, termasuk detail seperti nama, developer, genre, kategori, rating, dan harga. Dataset ini berfungsi sebagai basis data untuk membangun dan melatih sistem rekomendasi.

*   **Sumber Dataset:** [https://www.kaggle.com/datasets/nikdavis/steam-store-games](https://www.kaggle.com/datasets/nikdavis/steam-store-games)
*   **Ukuran Dataset Awal:** Dataset awal memiliki **[Jumlah Baris Awal]** baris dan **[Jumlah Kolom Awal]** kolom. (Isi angka spesifik dari output kode Anda).

Berikut adalah uraian variabel atau fitur pada dataset:

*   **appid:** ID unik untuk setiap game di Steam. Tipe data: integer.
*   **name:** Nama game. Tipe data: object (string).
*   **release_date:** Tanggal rilis game. Tipe data: object (string) atau datetime (setelah preproses).
*   **english:** Indikator (0 atau 1) apakah deskripsi game tersedia dalam bahasa Inggris. Tipe data: integer.
*   **developer:** Nama developer game. Tipe data: object (string).
*   **publisher:** Nama publisher game. Tipe data: object (string).
*   **platforms:** Platform yang didukung game (Windows, Mac, Linux), dipisahkan oleh ';'. Tipe data: object (string).
*   **required_age:** Batasan usia yang diperlukan untuk memainkan game. Tipe data: integer.
*   **categories:** Kategori game (misalnya, Single-player, Multi-player, Steam Cloud), dipisahkan oleh ';'. Tipe data: object (string).
*   **genres:** Genre game (misalnya, Action, Adventure, Indie), dipisahkan oleh ';'. Tipe data: object (string).
*   **steamspy_tags:** Tag yang diberikan oleh komunitas SteamSpy (misalnya, Great Soundtrack, Story Rich), dipisahkan oleh ';'. Tipe data: object (string).
*   **positive_ratings:** Jumlah rating positif yang diterima game. Tipe data: integer.
*   **negative_ratings:** Jumlah rating negatif yang diterima game. Tipe data: integer.
*   **average_playtime:** Rata-rata waktu bermain game (dalam menit). Tipe data: integer.
*   **median_playtime:** Median waktu bermain game (dalam menit). Tipe data: integer.
*   **owners:** Perkiraan jumlah pemilik game. Tipe data: object (string) dengan rentang.
*   **price:** Harga game. Tipe data: float.
*   **about_the_game:** Deskripsi singkat tentang game. Tipe data: object (string).
*   **background:** Tautan ke gambar latar belakang game. Tipe data: object (string).
*   **screenshots:** Tautan ke tangkapan layar game. Tipe data: object (string).

*   **Variabel Tambahan yang Dibuat:**
    *   **combined_content:** Kolom yang menggabungkan teks dari 'genres' dan 'categories' (sesuai implementasi model Anda). Tipe data: object (string).
    *   **content_length:** Panjang teks di kolom 'combined_content'. Tipe data: integer.
    *   **combined:** Kolom yang menggabungkan teks dari 'genres' dan 'developer' (tidak digunakan dalam model rekomendasi saat ini). Tipe data: object (string).

## Data Preparation

Tahap Data Preparation merupakan langkah krusial untuk memastikan data dalam kondisi yang bersih, konsisten, dan siap untuk digunakan dalam proses pemodelan. Pada tahap ini, dilakukan serangkaian manipulasi data untuk menangani missing value, outlier, dan format data yang tidak sesuai, sehingga meningkatkan kualitas data dan kinerja model rekomendasi.

**Proses Data Preparation:**

1.  **Menangani Missing Values:**
    *   **Identifikasi Missing Values:** Langkah pertama adalah memeriksa keberadaan missing value pada setiap kolom menggunakan `df.isnull().sum()`. Hasil pemeriksaan menunjukkan jumlah nilai kosong di setiap kolom.
        *   *Alasan Diperlukan:* Missing value dapat mengganggu proses analisis dan pemodelan, terutama pada perhitungan statistik dan operasi vektorisasi.
    *   **Penghapusan Kolom dengan Banyak Missing Value:** Kolom dengan proporsi missing value yang sangat tinggi (misalnya, di atas 50%) dihapus. Threshold ini ditentukan berdasarkan pertimbangan bahwa imputasi untuk kolom seperti itu mungkin tidak menghasilkan data yang akurat.
        *   *Alasan Diperlukan:* Kolom dengan mayoritas data hilang cenderung tidak memberikan informasi yang cukup untuk pemodelan yang efektif dan dapat menimbulkan bias jika dipaksakan untuk dipertahankan atau diimputasi secara sederhana.
    *   **Penghapusan Baris dengan Missing Value Penting:** Baris yang memiliki missing value pada kolom-kolom kunci yang esensial untuk pembuatan fitur teks (`genres`, `tags`, `about_the_game`) dihapus.
        *   *Alasan Diperlukan:* Kolom seperti `genres`, `tags`, dan `about_the_game` adalah sumber utama informasi konten untuk model berbasis konten. Missing value pada baris-baris ini membuat game tersebut tidak memiliki representasi konten yang memadai, sehingga lebih baik dihapus daripada diimputasi dengan data yang mungkin tidak relevan.
    *   **Imputasi Missing Values Sederhana:** Untuk kolom numerik tertentu (`required_age`, `price`), missing value diisi dengan nilai 0.
        *   *Alasan Diperlukan:* Kolom ini penting untuk analisis deskriptif atau potensi pengembangan model di masa depan. Imputasi dengan 0 dipilih sebagai pendekatan sederhana, dengan asumsi bahwa game tanpa nilai yang ditentukan untuk usia atau harga mungkin secara default dianggap gratis atau cocok untuk semua usia. Namun, perlu dicatat bahwa ini adalah asumsi yang bisa disesuaikan tergantung konteks bisnis.

2.  **Menangani Outliers:**
    *   **Deteksi Outliers:** Outliers pada kolom numerik kunci (`price`, `positive_ratings`, `negative_ratings`, `required_age`, `achievements`) dideteksi menggunakan metode IQR (Interquartile Range). Game dengan nilai di luar batas `Q1 - 1.5*IQR` dan `Q3 + 1.5*IQR` dianggap sebagai outlier.
        *   *Alasan Diperlukan:* Outlier, terutama pada metrik seperti rating atau harga, dapat mendistorsi perhitungan kemiripan dan memengaruhi kinerja model.
    *   **Penghapusan Outliers:** Baris yang teridentifikasi sebagai outlier pada kolom-kolom yang disebutkan di atas dihapus dari dataset.
        *   *Alasan Diperlukan:* Menghapus outlier dapat membantu model lebih fokus pada pola data yang umum dan mengurangi pengaruh nilai ekstrem yang tidak representatif.
    *   **Verifikasi Setelah Penghapusan:** Setelah penghapusan, dilakukan pengecekan ulang jumlah outlier pada kolom-kolom tersebut untuk memastikan proses berjalan efektif.

3.  **Normalisasi Nama Game:**
    *   Kolom `name` diubah menjadi tipe data string, spasi di awal dan akhir dihapus, dan semua teks dikonversi menjadi huruf kecil.
        *   *Alasan Diperlukan:* Normalisasi nama game penting untuk memastikan konsistensi saat mencari game input untuk rekomendasi. Ini menghindari masalah pencarian akibat perbedaan kapitalisasi atau spasi ekstra.

4.  **Reset Index DataFrame:**
    *   Setelah serangkaian operasi penghapusan baris, index DataFrame direset.
        *   *Alasan Diperlukan:* Operasi penghapusan baris menyebabkan index DataFrame menjadi tidak berurutan dan memiliki "lompatan". Reset index memastikan index berurutan kembali dari 0 hingga N-1, yang sangat penting untuk pencocokan indeks antara DataFrame dan matriks kemiripan (`cosine_sim`) saat mengambil rekomendasi.

5.  **Pembuatan Kolom Gabungan (`combined_content`):**
    *   Kolom baru bernama `combined_content` dibuat dengan menggabungkan teks dari kolom `genres` dan `categories`. Missing value pada kolom asli diisi dengan string kosong sebelum digabungkan.
        *   *Alasan Diperlukan:* Menggabungkan informasi dari kolom-kolom tekstual ini menciptakan satu fitur representasi konten yang lebih kaya untuk setiap game. Ini berfungsi sebagai input utama untuk proses ekstraksi fitur teks menggunakan TF-IDF.

*   **Ukuran DataFrame Setelah Pembersihan:** Setelah seluruh tahapan data preparation, DataFrame memiliki **[Jumlah Baris Akhir]** baris dan **[Jumlah Kolom Akhir]** kolom. (Isi angka spesifik dari output kode Anda).

## Modeling

Pada tahap pemodelan, sistem rekomendasi berbasis konten dibangun menggunakan representasi teks dan pengukuran kemiripan.

**Algoritma dan Tahapan Pemodelan:**

1.  **Ekstraksi Fitur Teks (TF-IDF - Term Frequency-Inverse Document Frequency):**
    *   Objek `TfidfVectorizer` diinisialisasi dengan `stop_words='english'` untuk menghapus kata-kata umum dalam bahasa Inggris yang kurang informatif.
    *   Metode `fit_transform()` diterapkan pada kolom `combined_content` dari DataFrame yang telah diproses. Ini akan:
        *   Mempelajari kosakata unik dari seluruh dokumen (game) dalam `combined_content`.
        *   Menghitung skor TF-IDF untuk setiap kata dalam setiap dokumen.
        *   Menghasilkan matriks `tfidf_matrix` di mana baris merepresentasikan game, kolom merepresentasikan kata, dan nilai adalah skor TF-IDF.
    *   *Alasan Penggunaan TF-IDF:* TF-IDF adalah teknik yang efektif untuk mengubah teks menjadi vektor numerik. Ini memberikan bobot yang lebih tinggi pada kata-kata yang sering muncul dalam sebuah dokumen tetapi jarang muncul di seluruh korpus, sehingga menonjolkan kata kunci unik untuk setiap game. Penggunaan `stop_words` membantu mengurangi dimensi matriks dan fokus pada kata-kata yang lebih bermakna.
    *   *Kelebihan:* Mampu menangkap pentingnya kata-kata unik, representasi numerik yang siap untuk perhitungan matematis.
    *   *Kekurangan:* Mengabaikan urutan kata (Bag-of-Words), sensitif terhadap variasi kata (membutuhkan stemming/lemmatization jika diperlukan, meskipun tidak dilakukan secara eksplisit di sini).

2.  **Pengukuran Kemiripan (Cosine Similarity):**
    *   Fungsi `cosine_similarity` dari `sklearn.metrics.pairwise` digunakan untuk menghitung matriks kemiripan antar vektor TF-IDF (`tfidf_matrix`).
    *   Outputnya adalah matriks `cosine_sim` berukuran N x N (di mana N adalah jumlah game), di mana setiap sel `(i, j)` berisi skor kemiripan kosinus antara game ke-i dan game ke-j. Skor berkisar antara 0 (tidak mirip sama sekali) hingga 1 (sangat mirip).
    *   *Alasan Penggunaan Cosine Similarity:* Cosine Similarity mengukur sudut antara dua vektor dalam ruang multidimensional. Dalam konteks TF-IDF, ini secara efektif mengukur seberapa mirip konten dua dokumen (game) terlepas dari panjangnya. Ini adalah metrik standar untuk mengukur kemiripan antar dokumen dalam sistem rekomendasi berbasis konten tekstual.
    *   *Kelebihan:* Efektif untuk data tekstual yang direpresentasikan sebagai vektor, tidak sensitif terhadap panjang dokumen.
    *   *Kekurangan:* Tidak mempertimbangkan besarnya vektor, hanya arahnya.

3.  **Fungsi Rekomendasi (`get_recommendations`):**
    *   Fungsi ini mengambil nama game sebagai input.
    *   Menormalisasi nama game input (huruf kecil, tanpa spasi ekstra) dan mencocokkannya dengan kolom `name` di DataFrame yang sudah dinormalisasi untuk menemukan indeks game tersebut. Penting untuk menggunakan DataFrame yang index-nya sudah di-reset agar sesuai dengan indeks di `cosine_sim`.
    *   Jika game tidak ditemukan, fungsi mencoba mencari nama game yang mirip sebagai saran.
    *   Jika game ditemukan, fungsi mengambil baris yang sesuai dari matriks `cosine_sim` (yang berisi skor kemiripan game input dengan semua game lain).
    *   Skor kemiripan diurutkan secara menurun, dan 10 game teratas (selain game itu sendiri) diambil sebagai rekomendasi.
    *   Fungsi mengembalikan DataFrame yang berisi nama dan genre dari game yang direkomendasikan serta indeks game tersebut.

*   **Improvement / Alternatif Algoritma:**
    *   *Saat ini, hanya satu pendekatan (TF-IDF + Cosine Similarity) yang diimplementasikan.*
    *   Sebagai bentuk improvement, dapat dilakukan hyperparameter tuning pada `TfidfVectorizer`. Parameter yang dapat dieksplorasi meliputi:
        *   `ngram_range`: Menggunakan n-gram (misalnya, `(1, 2)` untuk unigram dan bigram) dapat menangkap frasa atau kombinasi kata yang lebih bermakna daripada hanya kata tunggal.
        *   `max_df` dan `min_df`: Mengatur ambang batas frekuensi dokumen dapat membantu menghapus kata-kata yang terlalu umum atau terlalu jarang, sehingga meningkatkan kualitas representasi.
    *   Tuning dapat dilakukan secara manual dengan mencoba kombinasi parameter yang berbeda atau menggunakan teknik pencarian parameter seperti Grid Search atau Random Search jika cakupan parameter luas.

## Evaluation

Tahap evaluasi bertujuan untuk mengukur seberapa baik kinerja sistem rekomendasi dalam menyarankan game yang relevan. Metrik evaluasi kuantitatif digunakan untuk memberikan gambaran objektif tentang kualitas rekomendasi.

**Metrik Evaluasi:**

Proyek ini menggunakan tiga metrik evaluasi standar untuk sistem rekomendasi berbasis peringkat:

1.  **Precision@k:**
    *   **Penjelasan:** Mengukur proporsi item yang relevan dalam daftar `k` item teratas yang direkomendasikan.
    *   **Formula:** $Precision@k = \frac{\text{Jumlah item relevan dalam rekomendasi top-k}}{\text{k}}$
    *   **Cara Kerja:** Untuk setiap game yang direkomendasikan, dicek apakah game tersebut dianggap relevan berdasarkan definisi relevansi yang ditentukan. Kemudian dihitung rasio jumlah game relevan di posisi 1 hingga k terhadap total jumlah game di posisi 1 hingga k (yaitu, k). Nilai yang lebih tinggi menunjukkan bahwa sebagian besar rekomendasi teratas benar-benar relevan.

2.  **Recall@k:**
    *   **Penjelasan:** Mengukur proporsi total item relevan yang berhasil ditangkap dalam daftar `k` item teratas yang direkomendasikan.
    *   **Formula:** $Recall@k = \frac{\text{Jumlah item relevan dalam rekomendasi top-k}}{\text{Total jumlah item relevan dalam dataset}}$
    *   **Cara Kerja:** Dihitung jumlah game yang dianggap relevan dalam seluruh dataset (berdasarkan definisi relevansi). Kemudian dihitung rasio jumlah game relevan yang muncul di posisi 1 hingga k rekomendasi terhadap total jumlah game relevan yang ada. Nilai yang lebih tinggi menunjukkan bahwa sistem berhasil menemukan sebagian besar game yang relevan.

3.  **NDCG@k (Normalized Discounted Cumulative Gain at k):**
    *   **Penjelasan:** Metrik yang memperhitungkan relevansi item dan posisinya dalam daftar rekomendasi. Memberikan bobot yang lebih tinggi pada item relevan yang muncul di posisi yang lebih tinggi.
    *   **Formula (Dasar):** $DCG@k = \sum_{i=1}^k \frac{rel_i}{\log_2(i+1)}$, di mana $rel_i$ adalah skor relevansi item di posisi $i$.
    *   **Formula (Normalized):** $NDCG@k = \frac{DCG@k}{IDCG@k}$, di mana IDCG@k adalah DCG dari daftar relevan yang ideal (item paling relevan di posisi teratas).
    *   **Cara Kerja:** NDCG menghitung "gain" dari rekomendasi (misalnya, 1 jika relevan, 0 jika tidak relevan) dengan diskon logaritmik berdasarkan posisinya. Item yang relevan di posisi 1 memiliki diskon paling kecil, sedangkan item yang relevan di posisi yang lebih rendah memiliki diskon lebih besar. DCG kemudian dinormalisasi dengan IDCG (DCG maksimum yang mungkin didapatkan jika semua item relevan direkomendasikan di posisi paling atas). Nilai berkisar antara 0 hingga 1, di mana 1 menunjukkan rekomendasi yang sempurna.

**Definisi Relevansi untuk Evaluasi:**

Dalam konteks evaluasi ini, sebuah game dianggap "relevan" dengan game target jika memiliki setidaknya satu genre yang sama dengan game target. Definisi ini dipilih agar konsisten dengan fitur utama (genre dan kategori) yang digunakan dalam model berbasis konten.

**Hasil Proyek Berdasarkan Metrik Evaluasi:**

Evaluasi dilakukan untuk game target "**[Nama Game Target yang Anda Uji, misal: crow]**" dengan nilai `k = 10`.

*   **Precision@10:** **[Nilai Precision@10]**
    *   *Interpretasi:* Dari 10 game yang direkomendasikan teratas untuk "**[Nama Game Target]**", **[Nilai Precision@10 * 100]%** di antaranya memiliki setidaknya satu genre yang sama dengan game target.

*   **Recall@10:** **[Nilai Recall@10]**
    *   *Interpretasi:* Sistem berhasil menangkap **[Nilai Recall@10 * 100]%** dari total game relevan (yaitu, game dengan genre yang sama) di seluruh dataset dalam daftar 10 rekomendasi teratas.

*   **NDCG@10:** **[Nilai NDCG@10]**
    *   *Interpretasi:* Kualitas peringkat rekomendasi untuk game "**[Nama Game Target]**" adalah **[Nilai NDCG@10 * 100]%** dari daftar rekomendasi ideal pada posisi 10 teratas. Nilai ini menunjukkan seberapa baik game relevan ditempatkan di posisi yang lebih tinggi dalam daftar rekomendasi.

*   **Analisis Hasil:**
    *   Berdasarkan hasil metrik, jelaskan apakah nilai precision, recall, dan NDCG tinggi atau rendah.
    *   Hubungkan hasil metrik dengan bagaimana model bekerja. Misalnya, jika Precision tinggi tetapi Recall rendah, ini mungkin berarti model sangat baik dalam merekomendasikan beberapa game yang relevan di posisi atas, tetapi melewatkan banyak game relevan lainnya di seluruh dataset.
    *   Sebutkan keterbatasan evaluasi ini (misalnya, evaluasi hanya pada satu game target, definisi relevansi yang sederhana). Jelaskan bahwa untuk evaluasi yang lebih komprehensif, metrik ini harus dihitung rata-ratanya untuk banyak game target.
