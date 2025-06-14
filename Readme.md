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

Dataset yang digunakan dalam proyek ini adalah **Steam Store Games** yang diunduh dari [Kaggle](https://www.kaggle.com/datasets/nikdavis/steam-store-games). Dataset ini berisi informasi komprehensif tentang berbagai game yang tersedia di platform Steam, mencakup aspek-aspek seperti nama game, genre, pengembang, rating pengguna, waktu bermain, dan harga. Dataset ini menjadi sumber utama dalam membangun sistem rekomendasi berbasis konten.

- **Sumber Dataset:** [Steam Store Games - Kaggle](https://www.kaggle.com/datasets/nikdavis/steam-store-games)
- **Ukuran Dataset Awal:** Dataset awal terdiri dari **27075 baris** dan **18 kolom**.

### Kondisi Data Awal

Berdasarkan eksplorasi awal terhadap dataset:

- **Missing Values**:
  - Kolom `developer` memiliki **1** baris dengan nilai kosong.
  - Kolom `publisher` memiliki **14** baris dengan nilai kosong.
  - Beberapa kolom lain seperti `required_age`, `price`, dan `achievements` juga memiliki nilai kosong, namun jumlahnya lebih sedikit.
- **Outlier**:
  - Terdapat outlier pada kolom numerik seperti `price`, `positive_ratings`, `negative_ratings`, dan `required_age`. Outlier ini kemudian diidentifikasi dan diatasi menggunakan metode IQR pada tahap Data Preparation.

### Deskripsi Variabel Utama

Berikut adalah deskripsi kolom-kolom (fitur) yang terdapat dalam dataset dan digunakan dalam analisis:

| Nama Kolom         | Deskripsi                                                                 | Tipe Data |
|--------------------|---------------------------------------------------------------------------|-----------|
| `appid`            | ID unik game di platform Steam                                            | Integer   |
| `name`             | Nama game                                                                 | Object    |
| `release_date`     | Tanggal rilis game                                                        | Object    |
| `english`          | Indikator apakah game tersedia dalam Bahasa Inggris (0 = tidak, 1 = ya)  | Integer   |
| `developer`        | Nama pengembang game                                                      | Object    |
| `publisher`        | Nama penerbit game                                                        | Object    |
| `platforms`        | Platform yang didukung (Windows, Mac, Linux)                              | Object    |
| `required_age`     | Batasan usia minimum untuk memainkan game                                 | Integer   |
| `categories`       | Kategori game (misal: Single-player, Multi-player)                        | Object    |
| `genres`           | Genre game (misal: Action, Indie, Simulation)                             | Object    |
| `steamspy_tags`    | Tag komunitas dari SteamSpy                                               | Object    |
| `achievements`     | Jumlah pencapaian (achievements) dalam game                              | Integer   |
| `positive_ratings` | Jumlah rating positif                                                     | Integer   |
| `negative_ratings` | Jumlah rating negatif                                                     | Integer   |
| `average_playtime` | Rata-rata waktu bermain (menit)                                           | Integer   |
| `median_playtime`  | Median waktu bermain (menit)                                              | Integer   |
| `owners`           | Rentang jumlah pemilik game (misal: 20,000 - 50,000)                      | Object    |
| `price`            | Harga game (dalam satuan USD)                                             | Float     |

### Fitur Tambahan yang Dibuat

Beberapa fitur baru dibuat selama proses data preparation untuk mendukung proses pemodelan sistem rekomendasi:

| Nama Kolom         | Deskripsi                                                                 |
|--------------------|---------------------------------------------------------------------------|
| `combined`         | Gabungan teks dari `genres` dan `developer`                              |
| `combined_content` | Gabungan teks dari `genres` dan `categories`, digunakan untuk TF-IDF     |
| `content_length`   | Panjang teks (jumlah karakter) dari kolom `combined_content`             |

## Data Preparation

Tahap Data Preparation merupakan langkah krusial untuk memastikan data dalam kondisi yang bersih, konsisten, dan siap untuk digunakan dalam proses pemodelan. Pada tahap ini, dilakukan serangkaian manipulasi data untuk menangani missing value, outlier, dan format data yang tidak sesuai, sehingga meningkatkan kualitas data dan kinerja model rekomendasi.

## Data Preparation

Tahap *Data Preparation* merupakan langkah penting untuk memastikan data dalam kondisi bersih, konsisten, dan siap digunakan dalam proses pemodelan sistem rekomendasi berbasis konten. Pada tahap ini dilakukan beberapa proses untuk menghapus data yang tidak relevan, menangani missing value, mendeteksi dan menghapus outlier, serta membentuk fitur baru yang akan digunakan dalam pemodelan.

**Langkah-langkah yang dilakukan pada tahap ini meliputi:**

1. **Penghapusan Kolom yang Tidak Relevan:**
   - Kolom `appid` dihapus karena hanya berfungsi sebagai ID unik dan tidak memberikan informasi bermakna terhadap konten game.
   - *Alasan:* Tidak berkontribusi terhadap fitur yang dapat digunakan dalam sistem rekomendasi.
   - *Dampak:* Jumlah kolom berkurang satu tanpa kehilangan informasi konten penting.

2. **Penanganan Missing Value Secara Spesifik:**
   - Data yang memiliki missing value pada kolom `developer` dan `publisher` dihapus menggunakan `dropna(subset=['developer', 'publisher'])`.
   - *Alasan:* Developer dan publisher merupakan informasi penting dalam deskripsi game dan bisa berkontribusi dalam proses ekstraksi konten.
   - *Dampak:* Dataset menjadi lebih bersih dengan hanya menyisakan baris yang memiliki informasi lengkap untuk dua kolom penting tersebut.

3. **Konversi Tipe Data Numerik:**
   - Kolom `achievements`, `positive_ratings`, `negative_ratings`, `average_playtime`, `median_playtime`, dan `price` dikonversi menjadi tipe data float agar bisa digunakan dalam analisis numerik.
   - *Alasan:* Memastikan data bertipe numerik untuk memungkinkan perhitungan jarak, korelasi, dan analisis lainnya.
   - *Dampak:* Data menjadi kompatibel dengan metode pemodelan berbasis angka.

4. **Penanganan Outlier:**
   - Menggunakan metode IQR (*Interquartile Range*), outlier pada kolom `price`, `positive_ratings`, `negative_ratings`, `required_age`, dan `achievements` dihapus.
   - *Alasan:* Mengurangi pengaruh nilai ekstrem yang bisa menyebabkan bias dalam model.
   - *Dampak:* Dataset menjadi lebih representatif terhadap pola umum dari populasi data.

5. **Normalisasi Nama Game:**
   - Kolom `name` dinormalisasi menjadi huruf kecil dan dihapus spasi di awal/akhir.
   - *Alasan:* Konsistensi dalam pencarian dan pencocokan nama game.
   - *Dampak:* Menghindari duplikasi karena perbedaan kapitalisasi atau spasi.

6. **Pembuatan Fitur Gabungan `combined_content`:**
   - Kolom `combined_content` dibentuk dengan menggabungkan nilai dari kolom `genres` dan `categories`, yang sebelumnya diisi nilai kosong dengan string kosong jika ada missing value.
   - *Alasan:* Menyatukan informasi konten utama untuk digunakan dalam representasi berbasis teks (TF-IDF).
   - *Dampak:* Memberikan representasi teks tunggal yang kaya terhadap karakteristik konten tiap game.

7. **Pembuatan Fitur Tambahan `content_length`:**
   - Panjang karakter dari kolom `combined_content` dihitung dan disimpan dalam kolom baru `content_length`.
   - *Alasan:* Dapat digunakan untuk eksplorasi atau validasi panjang konten yang tersedia.
   - *Dampak:* Memberikan gambaran mengenai seberapa informatif setiap entri konten.

8. **Reset Index DataFrame:**
   - Setelah penghapusan baris karena missing value atau outlier, index di-reset ulang agar kembali konsisten dari 0 hingga N-1.
   - *Alasan:* Memastikan indeks tetap rapi dan sesuai saat digunakan dalam pemodelan atau pemanggilan ulang rekomendasi.
   - *Dampak:* Memudahkan proses identifikasi baris dan menghindari error akibat index yang tidak berurutan.

---

**Ukuran Dataset Setelah Pembersihan:**
- Setelah semua proses pembersihan dilakukan, ukuran akhir dataset menjadi **10823 baris dan 18 kolom**.

9. **Transformasi Teks Menjadi Representasi Numerik (TF-IDF):**
    * Setelah kolom `combined_content` selesai dibuat, teks dalam kolom tersebut diubah menjadi representasi numerik menggunakan teknik **TF-IDF (Term Frequency-Inverse Document Frequency)**.
    * TF-IDF memberikan bobot pada kata berdasarkan seberapa sering kata muncul dalam suatu dokumen dibandingkan dengan seluruh dokumen lainnya.

    ```python
    from sklearn.feature_extraction.text import TfidfVectorizer
    from sklearn.metrics.pairwise import cosine_similarity

    # Transformasi teks menjadi matriks TF-IDF
    tfidf = TfidfVectorizer(stop_words='english')
    tfidf_matrix = tfidf.fit_transform(df['combined_content'])

    # Hitung cosine similarity antar semua game berdasarkan konten
    cosine_sim = cosine_similarity(tfidf_matrix, tfidf_matrix)

    # Cetak dimensi hasil untuk verifikasi
    print(f"Shape of df: {df.shape}")
    print(f"Shape of tfidf_matrix: {tfidf_matrix.shape}")
    print(f"Shape of cosine_sim: {cosine_sim.shape}")
    ```

    *Alasan Diperlukan:* Representasi numerik ini memungkinkan pengukuran kemiripan antar game menggunakan metrik **cosine similarity**, yang menjadi dasar dari sistem rekomendasi berbasis konten.


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

Evaluasi dilakukan untuk game target "**super star blast**" dengan nilai `k = 10`.

*   **Precision@10:** 1.00
    *   *Interpretasi:* Hasil Precision@10 sebesar 1.00 menunjukkan bahwa **semua** (100%) dari 10 game teratas yang direkomendasikan untuk game "crow" dianggap relevan, yaitu memiliki setidaknya satu genre yang sama dengan "crow". Ini mengindikasikan bahwa rekomendasi yang diberikan di posisi paling atas sangat akurat dalam hal kesamaan genre.

*   **Recall@10:** 0.00
    *   *Interpretasi:* Hasil Recall@10 sebesar 0.00 berarti bahwa **tidak ada satu pun** (0%) dari total game relevan yang ada di seluruh dataset (game dengan genre yang sama dengan "crow") yang berhasil masuk ke dalam daftar 10 rekomendasi teratas.

*   **NDCG@10:** 1.00
    *   *Interpretasi:* Hasil NDCG@10 sebesar 1.00 menunjukkan bahwa kualitas peringkat rekomendasi untuk game "super star blast" adalah sempurna pada posisi 10 teratas, relatif terhadap daftar ideal yang hanya mempertimbangkan relevansi berdasarkan genre. Nilai 1.00 tercapai karena *jika* ada item relevan dalam 10 rekomendasi teratas, mereka ditempatkan di posisi yang optimal. Dikombinasikan dengan Precision 1.00, ini menunjukkan bahwa semua item dalam 10 rekomendasi adalah relevan (sesuai definisi relevansi) dan mereka diberi peringkat dengan cara yang ideal.

*   **Analisis Hasil:**
    Hasil evaluasi ini memberikan gambaran yang menarik dan sedikit kontradiktif. Precision@10 dan NDCG@10 yang bernilai 1.00 menunjukkan bahwa **untuk game yang direkomendasikan di posisi 1 hingga 10, semuanya dianggap relevan berdasarkan definisi relevansi (memiliki genre yang sama)**, dan penempatannya di posisi teratas sudah optimal. Namun, Recall@10 yang bernilai 0.00 menunjukkan bahwa **meskipun 10 game teratas relevan, mereka tidak mencakup game relevan lainnya yang mungkin ada di dataset** (game lain yang juga memiliki genre yang sama dengan "crow" tetapi tidak muncul di 10 teratas).

    Kemungkinan penyebab hasil ini:
    1.  **Jumlah Game Relevan Total Sangat Besar:** Mungkin ada sangat banyak game di dataset yang memiliki genre yang sama dengan "crow". Meskipun 10 rekomendasi teratas semuanya relevan, jumlah total game relevan jauh lebih besar dari 10, sehingga proporsi yang berhasil ditangkap (Recall) menjadi sangat rendah atau nol.
    2.  **Definisi Relevansi yang Luas:** Definisi relevansi (memiliki "setidaknya satu genre yang sama") mungkin terlalu luas. Matriks kemiripan kosinus mungkin mengidentifikasi game yang "paling" mirip (skor kosinus tertinggi) di antara semua game, dan 10 game teratas kebetulan memiliki genre yang sama. Namun, banyak game lain dengan genre yang sama mungkin memiliki skor kemiripan kosinus yang jauh lebih rendah dan tidak masuk ke dalam 10 teratas, meskipun mereka juga dianggap "relevan" oleh definisi kita.
    3.  **Fokus Model pada Genre:** Karena model Anda saat ini hanya menggunakan 'genres' dan 'categories' untuk `combined_content`, matriks kemiripan sangat didominasi oleh kesamaan genre dan kategori. Ini menjelaskan mengapa rekomendasi teratas memiliki genre yang sama (Precision 1.00).

    Secara keseluruhan, model ini tampaknya sangat baik dalam merekomendasikan game yang *paling* mirip kontennya (berdasarkan genre/kategori) di posisi teratas. Namun, Recall yang rendah menyarankan bahwa model mungkin tidak efisien dalam menemukan *semua* game relevan yang tersebar di seluruh dataset, terutama jika jumlah game relevan sangat besar. Untuk mendapatkan gambaran kinerja yang lebih lengkap, disarankan untuk menghitung rata-rata metrik ini untuk sejumlah game target yang lebih banyak.

*   **Keterbatasan Evaluasi:** Penting untuk diingat bahwa evaluasi ini dilakukan hanya pada satu game target ("super star blast") dan menggunakan definisi relevansi berbasis kesamaan genre sederhana. Kinerja model dapat bervariasi untuk game lain. Evaluasi yang lebih robust akan melibatkan perhitungan rata-rata metrik ini untuk sampel game target yang lebih representatif.

---Ini adalah bagian akhir laporan---
