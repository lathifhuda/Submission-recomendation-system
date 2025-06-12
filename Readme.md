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
*   **Ukuran Dataset Awal:** Dataset awal memiliki **27075** baris dan **18** kolom.

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

## Data Preparation

Tahap Data Preparation merupakan langkah krusial untuk memastikan data dalam kondisi yang bersih, konsisten, dan siap untuk digunakan dalam proses pemodelan. Pada tahap ini, dilakukan serangkaian manipulasi data untuk menangani missing value, outlier, format data yang tidak sesuai, serta penyesuaian fitur.

**Proses Data Preparation:**

1.  **Penanganan Missing Values Awal (Dropna Global):**
    *   Langkah pertama dalam menangani missing value adalah dengan menghapus baris yang mengandung nilai kosong pada kolom manapun menggunakan perintah `df.dropna()`.
        *   *Alasan Diperlukan:* Baris yang memiliki missing value pada kolom-kolom penting seperti `developer` atau `publisher` tidak memberikan informasi lengkap mengenai atribut kunci game. Penghapusan baris-baris ini pada tahap awal adalah pendekatan pembersihan cepat untuk memastikan setiap observasi memiliki informasi dasar yang esensial. Langkah ini efektif untuk mengurangi dataset dari baris yang kurang lengkap di berbagai atribut.
        *   *Dampak:* Langkah ini menghapus semua baris yang memiliki setidaknya satu nilai kosong di kolom manapun saat perintah dijalankan, termasuk baris dengan missing value pada kolom `developer` dan `publisher`.

2.  **Identifikasi dan Penghapusan Kolom dengan Banyak Missing Value:**
    *   Setelah `df.dropna()` awal, dilakukan pengecekan kembali jumlah missing value yang tersisa.
    *   Kolom dengan proporsi missing value yang sangat tinggi (di atas ambang batas 50%) diidentifikasi dan dihapus dari DataFrame.
        *   *Alasan Diperlukan:* Meskipun `df.dropna()` awal sudah menghapus banyak missing value, beberapa mungkin tersisa atau muncul kembali setelah operasi lain (misalnya, konversi tipe data dengan `errors='coerce'` di kode yang lengkap). Menghapus kolom dengan mayoritas data hilang memastikan dataset hanya berisi kolom yang memiliki informasi substansial dan relevan untuk analisis dan pemodelan.

3.  **Penghapusan Baris dengan Missing Value pada Kolom Penting yang Ada:**
    *   Didefinisikan daftar kolom yang dianggap penting untuk model (`genres`, `tags`, `about_the_game`).
    *   Dataset kemudian difilter untuk menghapus baris yang masih memiliki missing value pada kolom-kolom yang termasuk dalam daftar "penting" *dan masih ada* di DataFrame.
        *   *Alasan Diperlukan:* Kolom-kolom ini (`genres`, `tags`, `about_the_game`) sangat krusial sebagai sumber informasi konten untuk model berbasis teks. Menghapus baris yang masih memiliki missing value di sini memastikan bahwa setiap game yang digunakan untuk pemodelan memiliki data konten yang memadai untuk proses ekstraksi fitur teks.

4.  **Imputasi Missing Values pada Kolom Spesifik:**
    *   Missing value pada kolom `required_age` diisi dengan nilai 0 jika kolom tersebut masih ada di DataFrame.
    *   Missing value pada kolom `price` diisi dengan nilai 0 jika kolom tersebut masih ada di DataFrame.
        *   *Alasan Diperlukan:* Kolom numerik ini penting untuk analisis atau penggunaan fitur di masa mendatang. Mengisi missing value dengan 0 adalah pendekatan sederhana untuk memastikan tidak ada nilai kosong yang tersisa pada kolom yang akan digunakan. Pendekatan 0 dipilih sebagai nilai default yang mengindikasikan tidak ada batasan usia atau harga gratis.

5.  **Normalisasi Nama Kolom:**
    *   Nama-nama kolom diubah menjadi huruf kecil dan spasi diganti dengan underscore (`_`). (Berdasarkan kode Anda, langkah ini sebenarnya dilakukan lebih awal di notebook, tetapi untuk kelengkapan, bisa disebutkan di sini sebagai bagian dari pembersihan awal).
        *   *Alasan Diperlukan:* Normalisasi nama kolom memudahkan akses dan konsistensi dalam penulisan kode.

6.  **Penghapusan Kolom 'appid':**
    *   Kolom 'appid' dihapus dari DataFrame. (Seperti normalisasi nama kolom, ini juga dilakukan lebih awal di notebook).
        *   *Alasan Diperlukan:* Kolom 'appid' merupakan identifikasi unik dan tidak relevan untuk fitur konten yang digunakan dalam model ini.

7.  **Konversi Tipe Data Numerik:**
    *   Beberapa kolom dikonversi menjadi tipe data numerik (float atau integer) menggunakan `pd.to_numeric` dengan `errors='coerce'`. Kolom yang dikonversi meliputi `achievements`, `positive_ratings`, `negative_ratings`, `average_playtime`, `median_playtime`, dan `price`. (Langkah ini juga dilakukan lebih awal di notebook, setelah penghapusan `appid` dan sebelum pembuatan kolom `combined`).
        *   *Alasan Diperlukan:* Memastikan tipe data numerik yang tepat memungkinkan operasi matematis dan penggunaan kolom dalam algoritma yang memerlukan input numerik.

8.  **Pembuatan Kolom Gabungan 'combined' (genres dan developer):**
    *   Kolom baru bernama `combined` dibuat dengan menggabungkan teks dari kolom `genres` dan `developer`. Missing value diisi string kosong sebelum digabung. (Langkah ini dilakukan setelah konversi tipe data numerik).
        *   *Alasan Diperlukan:* Kolom ini dibuat sebagai fitur potensial untuk menggabungkan informasi genre dan pengembang, meskipun tidak digunakan dalam model rekomendasi berbasis konten yang ditunjukkan.

9.  **Menangani Outlier (Menggunakan IQR):**
    *   Outlier pada kolom numerik terpilih (`price`, `positive_ratings`, `negative_ratings`, `required_age`, `achievements`) dihapus menggunakan metode IQR. Baris dengan nilai di luar batas `Q1 - 1.5*IQR` dan `Q3 + 1.5*IQR` untuk kolom-kolom ini dihapus dari dataset.
        *   *Alasan Diperlukan:* Menghapus outlier membantu menghasilkan data yang lebih homogen dan mengurangi pengaruh nilai ekstrem, yang dapat meningkatkan akurasi pengukuran kemiripan.

10. **Normalisasi Nama Game:**
    *   Kolom `name` diubah menjadi tipe data string, spasi di awal dan akhir dihapus, dan semua teks dikonversi menjadi huruf kecil.
        *   *Alasan Diperlukan:* Normalisasi nama game penting untuk konsistensi saat mencari game input dan mencocokkannya dengan data.

11. **Reset Index DataFrame:**
    *   Setelah serangkaian operasi penghapusan baris, index DataFrame direset untuk memastikan index berurutan kembali dari 0 hingga N-1.
        *   *Alasan Diperlukan:* Reset index penting untuk pencocokan indeks antara DataFrame dan matriks kemiripan saat mengambil rekomendasi.

12. **Pembuatan Kolom Gabungan 'combined_content' (genres dan categories):**
    *   Kolom baru bernama `combined_content` dibuat dengan menggabungkan teks dari kolom `genres` dan `categories`. Missing value diisi string kosong sebelum digabungkan. Kolom ini akan menjadi input utama untuk TF-IDF.
        *   *Alasan Diperlukan:* Menggabungkan informasi dari kolom genre dan kategori menciptakan satu representasi konten yang lebih kaya untuk setiap game, yang akan digunakan oleh model rekomendasi berbasis konten.

*   **Ukuran DataFrame Setelah Pembersihan:** Setelah seluruh tahapan data preparation, DataFrame memiliki **[Jumlah Baris Akhir]** baris dan **[Jumlah Kolom Akhir]** kolom. (Isi angka spesifik dari output kode Anda setelah langkah terakhir).## Modeling

Pada tahap pemodelan, sistem rekomendasi berbasis konten dibangun menggunakan representasi teks dan pengukuran kemiripan.

## Modeling

Tahap pemodelan adalah inti dari pembangunan sistem rekomendasi. Pada tahap ini, data yang telah dibersihkan dan dipersiapkan digunakan untuk membangun model yang dapat menghitung kemiripan antar game dan menghasilkan rekomendasi. Model yang dikembangkan pada proyek ini menggunakan pendekatan Content-Based Filtering dengan memanfaatkan representasi tekstual game.

**Algoritma dan Tahapan Pemodelan:**

1.  **Representasi Teks Game (TF-IDF):**
    Seperti yang telah dijelaskan di bagian Data Preparation, kolom `combined_content` (gabungan genre dan kategori game) telah diubah menjadi matriks numerik menggunakan teknik TF-IDF (Term Frequency-Inverse Document Frequency). Proses ini menghasilkan matriks `tfidf_matrix` di mana setiap baris merepresentasikan sebuah game dan setiap kolom merepresentasikan sebuah kata dari kosakata unik dataset, dengan nilai sel menunjukkan bobot TF-IDF kata tersebut dalam game.

2.  **Pengukuran Kemiripan Antar Game (Cosine Similarity):**
    Setelah representasi vektor dari setiap game diperoleh, langkah selanjutnya adalah mengukur seberapa mirip setiap game satu sama lain. Untuk ini, digunakan **Cosine Similarity**.
    *   `sklearn.metrics.pairwise.cosine_similarity()` dihitung pada `tfidf_matrix`.
    *   *Alasan Penggunaan Cosine Similarity:* Cosine Similarity adalah metrik yang tepat untuk data vektor yang dihasilkan oleh TF-IDF. Ini mengukur kosinus sudut antara dua vektor, yang secara efektif menentukan seberapa mirip arah kedua vektor tersebut di ruang multidimensional. Dalam konteks ini, skor kemiripan kosinus yang tinggi antara dua game menunjukkan bahwa vektor TF-IDF mereka memiliki arah yang serupa, artinya konten tekstual (genre dan kategori) mereka sangat mirip. Metrik ini relatif tidak terpengaruh oleh panjang dokumen (jumlah kata) dan berfokus pada proporsi kata kunci yang sama.
    *   *Kelebihan Cosine Similarity:* Efektif dalam menentukan kemiripan antar dokumen berdasarkan konten kata, robust terhadap perbedaan panjang dokumen.
    *   *Kekurangan Cosine Similarity:* Tidak mempertimbangkan besaran vektor (hanya arah), dapat kurang efektif jika perbedaan besaran vektor juga penting.

    Hasil dari perhitungan ini adalah matriks `cosine_sim`, di mana `cosine_sim[i][j]` adalah skor kemiripan antara game ke-i dan game ke-j. Matriks ini adalah dasar untuk menemukan game serupa.

3.  **Fungsi Penghasil Rekomendasi (`get_recommendations`):**
    Sebuah fungsi Python bernama `get_recommendations` dibuat untuk mengotomatisasi proses mendapatkan rekomendasi.
    *   Fungsi ini mengambil **nama game** sebagai input, matriks `cosine_sim`, dan jumlah rekomendasi yang diinginkan (`top_n`).
    *   Di dalamnya, nama game input dinormalisasi (menjadi huruf kecil, tanpa spasi ekstra) dan dicari indeksnya dalam DataFrame `df` yang sudah diproses (dan indexnya sudah di-reset).
    *   Jika game tidak ditemukan, fungsi mencoba mencari game dengan nama yang mirip sebagai saran kepada pengguna.
    *   Jika game ditemukan, fungsi mengambil baris yang sesuai dari matriks `cosine_sim` berdasarkan indeks game target. Baris ini berisi skor kemiripan game target dengan semua game lain.
    *   Skor kemiripan diurutkan secara menurun, dan indeks dari `top_n` game dengan skor kemiripan tertinggi (selain game target itu sendiri) diekstrak.
    *   Fungsi mengembalikan DataFrame yang berisi nama dan genre dari game-game yang direkomendasikan, serta daftar indeks game tersebut. Penggunaan `.iloc` dengan indeks yang sesuai dan `.reset_index(drop=True)` memastikan output yang bersih.

*   **Improvement / Alternatif Algoritma:**
    Model yang diimplementasikan saat ini adalah pendekatan dasar berbasis konten dengan TF-IDF dan Cosine Similarity. Untuk meningkatkan kinerja atau menjelajahi opsi lain, beberapa area improvement dapat dipertimbangkan:
    *   **Hyperparameter Tuning TF-IDF:** Melakukan tuning pada parameter `TfidfVectorizer` seperti `ngram_range` (misal, (1, 2) untuk menyertakan bigram), `max_df`, atau `min_df` dapat menghasilkan representasi teks yang lebih optimal dan berpotensi meningkatkan kualitas kemiripan.
    *   **Fitur Konten Lebih Kaya:** Memasukkan kolom tekstual lain yang ada di dataset (misalnya, `steamspy_tags` atau bahkan deskripsi game penuh jika tersedia setelah preproses) ke dalam `combined_content` dapat memberikan representasi game yang lebih detail dan akurat.
    *   **Metode Pengukuran Kemiripan Lain:** Meskipun Cosine Similarity adalah pilihan umum, metrik lain seperti Euclidean Distance (meskipun kurang umum untuk TF-IDF) bisa dieksplorasi, tergantung karakteristik data.
    *   **Alternatif Algoritma:** Untuk perbandingan atau solusi alternatif, dapat dipertimbangkan:
        *   **Collaborative Filtering:** Membangun model berbasis interaksi pengguna (rating, playtime) jika data tersebut dianggap relevan untuk preferensi pengguna yang lebih personal.
        *   **Teknik Embedding Teks Lanjutan:** Menggunakan metode seperti Word2Vec, GloVe, atau FastText untuk mendapatkan representasi vektor kata yang dapat digabungkan untuk merepresentasikan game.

*   **Contoh Hasil Rekomendasi:**
    Untuk memberikan gambaran mengenai hasil rekomendasi yang dihasilkan oleh model, berikut adalah contoh output Top-10 rekomendasi untuk game target "**super star blast**" :
    ### Contoh Hasil Rekomendasi

Berikut adalah contoh 10 game teratas yang direkomendasikan untuk game target "**super star blast**" (ganti nama game target jika relevan):

| Peringkat | Nama Game                        | Genre                                   |
|-----------|----------------------------------|-----------------------------------------|
| 1         | capsule force                    | Action;Indie                            |
| 2         | defenders of the last colony     | Action;Indie                            |
| 3         | mactabilis                       | Action;Indie                            |
| 4         | aces of the luftwaffe - squadron | Action;Indie                            |
| 5         | shock troopers                   | Action                                  |
| 6         | heartz: co-hope puzzles          | Action;Adventure;Indie                  |
| 7         | cloudrift                        | Action;Casual;Indie                     |
| 8         | urban justice                    | Action;Adventure;Casual;Indie           |
| 9         | brawl                            | Action;Indie                            |
| 10        | bacterium / 生命之旅               | Action;Adventure;Indie;RPG;Simulation   |


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

Evaluasi dilakukan untuk game target "**crow**" dengan nilai `k = 10`.

*   **Precision@10:** 1.00
    *   *Interpretasi:* Hasil Precision@10 sebesar 1.00 menunjukkan bahwa **semua** (100%) dari 10 game teratas yang direkomendasikan untuk game "crow" dianggap relevan, yaitu memiliki setidaknya satu genre yang sama dengan "crow". Ini mengindikasikan bahwa rekomendasi yang diberikan di posisi paling atas sangat akurat dalam hal kesamaan genre.

*   **Recall@10:** 0.00
    *   *Interpretasi:* Hasil Recall@10 sebesar 0.00 berarti bahwa **tidak ada satu pun** (0%) dari total game relevan yang ada di seluruh dataset (game dengan genre yang sama dengan "crow") yang berhasil masuk ke dalam daftar 10 rekomendasi teratas.

*   **NDCG@10:** 1.00
    *   *Interpretasi:* Hasil NDCG@10 sebesar 1.00 menunjukkan bahwa kualitas peringkat rekomendasi untuk game "crow" adalah sempurna pada posisi 10 teratas, relatif terhadap daftar ideal yang hanya mempertimbangkan relevansi berdasarkan genre. Nilai 1.00 tercapai karena *jika* ada item relevan dalam 10 rekomendasi teratas, mereka ditempatkan di posisi yang optimal. Dikombinasikan dengan Precision 1.00, ini menunjukkan bahwa semua item dalam 10 rekomendasi adalah relevan (sesuai definisi relevansi) dan mereka diberi peringkat dengan cara yang ideal.

*   **Analisis Hasil:**
    Hasil evaluasi ini memberikan gambaran yang menarik dan sedikit kontradiktif. Precision@10 dan NDCG@10 yang bernilai 1.00 menunjukkan bahwa **untuk game yang direkomendasikan di posisi 1 hingga 10, semuanya dianggap relevan berdasarkan definisi relevansi (memiliki genre yang sama)**, dan penempatannya di posisi teratas sudah optimal. Namun, Recall@10 yang bernilai 0.00 menunjukkan bahwa **meskipun 10 game teratas relevan, mereka tidak mencakup game relevan lainnya yang mungkin ada di dataset** (game lain yang juga memiliki genre yang sama dengan "crow" tetapi tidak muncul di 10 teratas).

    Kemungkinan penyebab hasil ini:
    1.  **Jumlah Game Relevan Total Sangat Besar:** Mungkin ada sangat banyak game di dataset yang memiliki genre yang sama dengan "crow". Meskipun 10 rekomendasi teratas semuanya relevan, jumlah total game relevan jauh lebih besar dari 10, sehingga proporsi yang berhasil ditangkap (Recall) menjadi sangat rendah atau nol.
    2.  **Definisi Relevansi yang Luas:** Definisi relevansi (memiliki "setidaknya satu genre yang sama") mungkin terlalu luas. Matriks kemiripan kosinus mungkin mengidentifikasi game yang "paling" mirip (skor kosinus tertinggi) di antara semua game, dan 10 game teratas kebetulan memiliki genre yang sama. Namun, banyak game lain dengan genre yang sama mungkin memiliki skor kemiripan kosinus yang jauh lebih rendah dan tidak masuk ke dalam 10 teratas, meskipun mereka juga dianggap "relevan" oleh definisi kita.
    3.  **Fokus Model pada Genre:** Karena model Anda saat ini hanya menggunakan 'genres' dan 'categories' untuk `combined_content`, matriks kemiripan sangat didominasi oleh kesamaan genre dan kategori. Ini menjelaskan mengapa rekomendasi teratas memiliki genre yang sama (Precision 1.00).

    Secara keseluruhan, model ini tampaknya sangat baik dalam merekomendasikan game yang *paling* mirip kontennya (berdasarkan genre/kategori) di posisi teratas. Namun, Recall yang rendah menyarankan bahwa model mungkin tidak efisien dalam menemukan *semua* game relevan yang tersebar di seluruh dataset, terutama jika jumlah game relevan sangat besar. Untuk mendapatkan gambaran kinerja yang lebih lengkap, disarankan untuk menghitung rata-rata metrik ini untuk sejumlah game target yang lebih banyak.

*   **Keterbatasan Evaluasi:** Penting untuk diingat bahwa evaluasi ini dilakukan hanya pada satu game target ("crow") dan menggunakan definisi relevansi berbasis kesamaan genre sederhana. Kinerja model dapat bervariasi untuk game lain. Evaluasi yang lebih robust akan melibatkan perhitungan rata-rata metrik ini untuk sampel game target yang lebih representatif.

---Ini adalah bagian akhir laporan---
