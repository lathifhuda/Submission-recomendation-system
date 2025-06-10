Laporan Proyek Sistem Rekomendasi
Game Recommendation System
    Nama: Lathif Nurma Huda
    Email: MC604D5Y0872
    ID Dicoding: MC604D5Y0872

1. Projek Overview

Proyek ini bertujuan untuk membangun sebuah sistem rekomendasi game menggunakan pendekatan Content-Based Filtering. Sistem ini akan merekomendasikan game lain yang serupa dengan game yang dipilih pengguna berdasarkan fitur-fitur teks yang menggambarkan konten game, seperti genre, kategori, tag, dan deskripsi game.

Dataset yang digunakan adalah "Steam Store Games" dari Kaggle yang berisi informasi tentang berbagai game yang tersedia di platform Steam. Dataset ini dipilih karena kaya akan metadata tekstual yang relevan untuk membangun sistem rekomendasi berbasis konten.

Sistem Content-Based Filtering bekerja dengan menganalisis deskripsi item (game) yang disukai pengguna di masa lalu dan kemudian mencari item lain di katalog yang memiliki deskripsi serupa. Dalam proyek ini, fitur teks game akan diubah menjadi representasi numerik menggunakan TF-IDF (Term Frequency-Inverse Document Frequency), dan kemiripan antara game akan diukur menggunakan Cosine Similarity.

Hasil dari proyek ini adalah model rekomendasi yang dapat memberikan daftar game yang paling mirip dengan game input, serta evaluasi kinerja model menggunakan metrik seperti Precision@k, Recall@k, dan NDCG@k.
2. Data Understanding

Dataset "Steam Store Games" diunduh dari Kaggle dengan ID nikdavis/steam-store-games. Setelah diunduh dalam format zip, dataset diekstraksi dan file steam.csv dibaca ke dalam DataFrame pandas. Mengingat ukuran dataset yang cukup besar, sampel acak sebanyak 20.000 baris diambil untuk mempercepat proses pengembangan dan pengujian model awal.

Dataset memiliki beberapa kolom, termasuk:
    appid: ID unik untuk setiap game.
    name: Nama game.
    release_date: Tanggal rilis game.
    english: Menandakan apakah deskripsi game dalam bahasa Inggris (nilai 1).
    developer: Pengembang game.
    publisher: Penerbit game.
    platforms: Platform di mana game tersedia (Windows, macOS, Linux).
    required_age: Batasan usia yang disarankan.
    categories: Kategori game (misalnya, Single-player, Multi-player, Co-op).
    genres: Genre game (misalnya, Action, Indie, RPG).
    steamspy_tags: Tag yang diberikan oleh SteamSpy.
    achievements: Jumlah achievements dalam game.
    positive_ratings: Jumlah rating positif.
    negative_ratings: Jumlah rating negatif.
    average_playtime: Rata-rata waktu bermain (dalam menit).
    median_playtime: Median waktu bermain (dalam menit).
    owners: Rentang jumlah pemilik game.
    price: Harga game.
    initial_price: Harga awal game.
    discount: Diskon saat ini.
    ccu: Concurrent users.
    about_the_game: Deskripsi singkat tentang game.
    supported_languages: Bahasa yang didukung.
    full_audio_languages: Bahasa dengan dukungan audio penuh.
    header_image: URL gambar header game.
    website: Situs web resmi game.
    pc_requirements, mac_requirements, linux_requirements: Persyaratan sistem.
    developers, publishers: Kolom duplikat dari developer dan publisher.

Beberapa insight awal dari EDA:
    Nama kolom distandarisasi menjadi huruf kecil dan spasi diganti underscore untuk kemudahan akses.
    Wordcloud pada kolom genres dan categories menunjukkan genre dan kategori yang paling umum dalam dataset, seperti 'Indie', 'Action', 'Adventure', 'Casual', 'Single-player', 'Multi-player'.
    Pengecekan outlier pada kolom numerik seperti price, required_age, dan achievements mengindikasikan adanya nilai ekstrem yang perlu ditangani.
    Distribusi panjang konten gabungan dari kolom teks menunjukkan variasi dalam seberapa detail deskripsi game yang tersedia.
    Terdapat missing values di beberapa kolom, yang paling banyak ada di kolom-kolom seperti ccu, owners, price, website, supported_languages, full_audio_languages, dan persyaratan sistem (pc_requirements, dll.).

3. Data Preparation

Langkah-langkah data preparation dilakukan untuk membersihkan data dan mempersiapkannya untuk proses modeling:
    Penanganan Missing Values:
        Kolom dengan lebih dari 50% missing values dihapus, seperti ccu.
        Baris yang memiliki missing values pada kolom-kolom penting untuk konten (genres, tags, about_the_game) dihapus (dropna). Ini dilakukan karena nilai-nilai ini krusial untuk representasi teks game.
        Nilai kosong di kolom required_age dan price diimputasi dengan 0, dengan asumsi game tanpa nilai tersebut adalah untuk semua umur atau gratis.
    Penanganan Outlier:
        Outlier pada kolom numerik yang relevan (price, positive_reviews (jika ada setelah standarisasi nama kolom), negative_reviews (jika ada), required_age, achievements) diidentifikasi menggunakan metode IQR.
        Baris yang mengandung outlier pada kolom-kolom tersebut dihapus dari DataFrame.
    Pembuatan Kolom Teks Gabungan:
        (Berdasarkan kode setelah pengujian fungsi rekomendasi pertama) Kolom teks yang relevan untuk model berbasis konten (genres, categories, tags, about_the_game) digabungkan menjadi satu kolom baru (dalam kode disebut combined). Nilai kosong diisi dengan string kosong sebelum digabung.
        (Berdasarkan kode Modeling pertama) Kolom genres juga digunakan secara terpisah untuk membuat kolom teks gabungan (combined_content) awal. Ini menunjukkan adanya iterasi dalam pemilihan fitur teks.

Setelah proses pembersihan dan persiapan data, ukuran DataFrame menjadi lebih kecil, dan jumlah missing values pada kolom-kolom yang relevan telah berkurang signifikan atau dihilangkan.
4. Modeling
Model rekomendasi yang dibangun menggunakan pendekatan Content-Based Filtering dengan langkah-langkah berikut:
    Representasi Teks (TF-IDF):
        Kolom teks gabungan (combined_content pada langkah pertama modeling, atau combined pada langkah modeling selanjutnya) yang berisi informasi tentang genre, kategori, tag, dan deskripsi game digunakan sebagai input.
        TfidfVectorizer dari scikit-learn digunakan untuk mengubah teks ini menjadi matriks TF-IDF. Matriks ini merepresentasikan setiap game sebagai vektor di mana setiap dimensi mewakili kata atau term dan nilainya mencerminkan kepentingan kata tersebut dalam game dibandingkan dengan seluruh dataset. Stop words bahasa Inggris dihapus selama proses ini. Dalam implementasi terakhir (setelah pengujian pertama), max_features diatur ke 10.000 untuk membatasi ukuran matriks TF-IDF.
    Pengukuran Kemiripan (Cosine Similarity):
        Setelah mendapatkan matriks TF-IDF, cosine_similarity dari scikit-learn dihitung antara setiap pasangan game.
        Hasilnya adalah matriks kemiripan kosinus (cosine_sim) di mana nilai pada baris i dan kolom j menunjukkan seberapa mirip game i dan game j berdasarkan representasi TF-IDF mereka. Semakin tinggi nilainya (mendekati 1), semakin mirip kedua game tersebut.
    Fungsi Rekomendasi:
        Dibuat sebuah fungsi get_recommendations yang menerima nama game input dan jumlah rekomendasi yang diinginkan (top_n).
        Fungsi ini mencari game input dalam DataFrame (setelah menormalisasi nama game menjadi huruf kecil dan menghapus spasi).
        Jika game ditemukan, fungsi mengambil baris kemiripan yang sesuai dari matriks cosine_sim.
        Skor kemiripan diurutkan, dan indeks top_n game dengan skor kemiripan tertinggi (tidak termasuk game itu sendiri) diambil.
        Fungsi mengembalikan DataFrame yang berisi nama dan genre dari game yang direkomendasikan. Logika penanganan game yang tidak ditemukan dan pemberian saran nama game yang mirip juga diimplementasikan.

Catatan: Berdasarkan kode, matriks cosine_sim yang sebenarnya digunakan oleh fungsi get_recommendations adalah yang dihitung di blok pertama modeling, yang hanya menggunakan genres. Meskipun ada langkah selanjutnya yang membuat kolom combined yang lebih kaya dan objek TfidfVectorizer baru, matriks cosine_sim tidak dihitung ulang menggunakan fitur teks yang diperluas tersebut sebelum fungsi rekomendasi dipanggil untuk evaluasi. Selain itu, ada baris kode yang menimpa kolom name dengan appid, yang akan menyebabkan fungsi get_recommendations tidak berfungsi dengan nama game setelah baris tersebut dieksekusi.
5. Evaluation

Evaluasi dilakukan untuk mengukur kinerja model rekomendasi menggunakan game target spesifik ("sparky's hunt") pada k=10. Metrik yang digunakan adalah:
    Precision@k: Mengukur proporsi rekomendasi di k teratas yang relevan.
    Recall@k: Mengukur proporsi item relevan yang berhasil direkomendasikan di k teratas.
    NDCG@k: Mengukur relevansi rekomendasi dengan mempertimbangkan posisinya dalam daftar k teratas.

Item dianggap relevan jika memiliki setidaknya satu genre yang sama dengan game target.

Langkah-langkah evaluasi:
    Pilih game target ("sparky's hunt").
    Panggil fungsi get_recommendations untuk mendapatkan 10 rekomendasi teratas untuk game target.
    Identifikasi semua game di dataset yang memiliki genre yang sama dengan game target (ini adalah set item relevan).
    Hitung nilai Precision@10, Recall@10, dan NDCG@10 dengan membandingkan daftar game yang direkomendasikan dengan daftar game yang relevan.

Hasil evaluasi (nilai Precision@10, Recall@10, dan NDCG@10) akan memberikan indikasi seberapa baik model (yang saat ini hanya berbasis genre) merekomendasikan game yang memiliki genre serupa dengan game target spesifik yang dievaluasi.



