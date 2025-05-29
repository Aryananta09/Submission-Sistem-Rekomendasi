# Laporan Proyek Machine Learning - Muhammad Awwal Aryananta

## Project Overview

Dalam era informasi digital saat ini, volume data terus meningkat secara eksponensial, termasuk dalam industri penerbitan dan penjualan buku. Pengguna kini dihadapkan pada ribuan pilihan buku, yang sering kali membuat mereka kesulitan menemukan buku yang sesuai dengan minat dan preferensi mereka. Oleh karena itu, sistem rekomendasi menjadi solusi penting dalam membantu pengguna menemukan konten yang relevan secara efisien.

Sistem rekomendasi buku berperan besar dalam meningkatkan kepuasan pengguna serta mendorong keterlibatan dan penjualan. Menurut Ricci et al. (2011), sistem rekomendasi dapat meningkatkan pengalaman pengguna dengan menyediakan saran yang dipersonalisasi berdasarkan histori interaksi atau kemiripan konten [1]. Dengan pendekatan content-based filtering dan collaborative filtering, sistem dapat mempelajari pola preferensi pengguna dan memberikan rekomendasi yang akurat dan relevan. Sistem rekomendasi sendiri merupakan aplikasi yang dirancang untuk memberikan saran terhadap item tertentu guna membantu pengambilan keputusan pengguna [2]. 

Pendekatan content-based filtering berfokus pada perilaku masa lalu pengguna untuk mengidentifikasi pola yang dapat digunakan dalam memberikan saran item yang serupa [3], sedangkan collaborative filtering mengandalkan opini atau preferensi dari pengguna lain sebagai acuan dalam menyaring item yang relevan [4].

Proyek ini bertujuan membangun sistem rekomendasi buku dengan memanfaatkan dataset publik dari Book-Crossing, yang mencakup informasi buku, pengguna, dan rating. Melalui pemrosesan data, analisis eksploratif, dan pengembangan dua pendekatan rekomendasi (content-based dan collaborative filtering dengan neural network), sistem ini diharapkan mampu memberikan rekomendasi buku yang lebih personal dan berkualitas.


## Business Understanding

Dalam mengembangkan sistem rekomendasi, penting untuk terlebih dahulu memahami permasalahan dari sisi kebutuhan pengguna, konteks penggunaan, serta keterbatasan sistem yang ada saat ini. Klarifikasi masalah diperlukan agar solusi yang dirancang benar-benar menjawab kebutuhan pengguna dan memberikan dampak nyata.

Dalam konteks ini, banyaknya pilihan buku yang tersedia membuat pengguna kesulitan menemukan bacaan yang sesuai dengan preferensi mereka. Hal ini mendorong perlunya sistem rekomendasi yang mampu memberikan saran buku secara personal dan relevan.

Bagian laporan ini mencakup:

### Problem Statements

-  Bagaimana membangun sistem rekomendasi buku yang dipersonalisasi berdasarkan kesamaan konten (judul, penulis, penerbit) dengan buku-buku yang pernah disukai pengguna?
- Bagaimana memberikan rekomendasi buku yang relevan kepada pengguna, meskipun buku tersebut belum pernah dibaca, dengan memanfaatkan histori rating dari pengguna lain yang memiliki preferensi serupa?

### Goals

- Menghasilkan rekomendasi buku yang dipersonalisasi menggunakan pendekatan content-based filtering, dengan mempertimbangkan fitur-fitur seperti penulis, judul, dan penerbit.
- Menghasilkan rekomendasi buku menggunakan pendekatan collaborative filtering, sehingga pengguna dapat menemukan buku yang belum pernah mereka baca, namun disukai oleh pengguna lain dengan minat serupa.

### Solution statements

- Solution 1: Content-Based Filtering.
Sistem akan mencari buku yang memiliki fitur konten mirip dengan buku-buku yang telah disukai oleh pengguna. Pendekatan ini dilakukan dengan menggunakan representasi berbasis TF-IDF atau cosine similarity dari metadata buku seperti judul, penulis, dan penerbit.

- Solution 2: Collaborative Filtering dengan Neural Network.
Sistem akan menggunakan embedding-based model (dengan TensorFlow/Keras) untuk mempelajari representasi dari pengguna dan buku berdasarkan interaksi (rating), dan memprediksi kemungkinan seorang pengguna menyukai suatu buku yang belum pernah dibaca berdasarkan pola rating dari pengguna lain.

## Data Understanding
Dataset yang digunakan dalam proyek ini adalah Book Recommendation Dataset yang tersedia secara publik melalui platform Kaggle. Dataset ini dapat diakses melalui tautan berikut: [Book Recommendation Dataset](https://www.kaggle.com/datasets/arashnic/book-recommendation-dataset)

Dataset ini mencakup tiga file utama yang berisi informasi terkait buku, pengguna, dan rating yang diberikan pengguna terhadap buku. Secara keseluruhan, dataset ini terdiri dari lebih dari 270.000 entri rating, 1 juta data pengguna, dan lebih dari 270.000 judul buku. Namun, karena adanya inkonsistensi dan data duplikat, sehingga perlu dilakukan pembersihan serta penyaringan untuk mendapatkan data yang relevan dan valid sebelum membangun sistem rekomendasi.

Berikut adalah penjelasan mengenai masing-masing file beserta variabel yang terdapat di dalamnya:
1. Books.csv (Buku)
    - ISBN: ID unik dari buku.
    - Book-Title: Judul buku.
    - Book-Author: Nama penulis buku.
    - Year-Of-Publication: Tahun terbit buku.
    - Publisher: Nama penerbit buku.
    - Image-URL-S: URL gambar sampul buku ukuran kecil.
    - Image-URL-M: URL gambar sampul buku ukuran sedang.
    - Image-URL-L: URL gambar sampul buku ukuran kecil

2. Users.csv (Pengguna)
    - User-ID: ID unik pengguna.
    - Location: Lokasi pengguna (format: kota, negara bagian, negara).
    - Age: Usia pengguna (dalam beberapa kasus terdapat nilai yang tidak valid atau kosong).

3. Ratings.csv (Penilaian Pengguna)
    - User-ID: ID unik pengguna.
    - ISBN: ID buku yang diberi rating.
    - Book-Rating: Skor penilaian dari pengguna, berkisar dari 0 (tidak memberi rating) hingga 10 (sangat menyukai).

### Exploratory Data Analysis (EDA)
#### EDA pada data buku
![Output dari books.info](https://github.com/user-attachments/assets/1818c5b8-c43d-445c-9b8c-5482da2c6b49)

Dapat dilihat dari gambar diatas, data buku pada dataset ini terdiri dari 271.360 jumlah baris data, dan 8 kolom yang seluruh tipe datanya merupakan object. Beberapa kolom terlihat memiliki beberapa missing value yang nantinya akan diproses lebih lanjut. Selanjutnya, saya menampilkan beberapa jumlah kolom yang saya rasa relevan untuk hasil analisis, seperti author yang berjumlah 102.022 author unik, tahun publikasi sebanyak 202 tahun unik, dan publisher sebanyak 16.807 publisher unik.

![Bar Chart Top Authors](https://github.com/user-attachments/assets/5ee0da2e-e390-457f-81d4-f35814a71dac)

Dari gambar diatas, dapat dilihat bahwa visualisasi grafik dengan bar chart ini dilakukan untuk menampilkan jumlah 20 author dengan jumlah buku terbanyak dari 102.022 author yang ada. Di nomor 1 ada Agatha Christie, disusul William Shakespeare, Stephen King, dan seterusnya. Popularitas author ini dapat dimanfaatkan sebagai fitur dalam sistem rekomendasi untuk mengidentifikasi penulis yang cenderung disukai banyak pembaca. Hal ini berguna terutama pada pendekatan content-based filtering dalam merekomendasikan buku dari penulis serupa atau yang sering dibaca.

![Distribusi Published Year](https://github.com/user-attachments/assets/fb825706-4ef6-460d-b41f-1f4cb9465c84)

Berdasarkan visualisasi di atas, terlihat bahwa distribusi tahun publikasi buku sangat tidak merata, dengan mayoritas data terkonsentrasi pada rentang tahun modern, sementara sisanya tersebar secara ekstrem pada tahun-tahun tidak realistis. Karena adanya rentang tahun yang sangat lebar dan ketidakwajaran nilai pada sebagian besar data, fitur year_published kemungkinan tidak akan digunakan dalam model sistem rekomendasi.

![Pie Chart Publisher](https://github.com/user-attachments/assets/7e331b37-dee0-473c-a011-3405316c3492)

Visualisasi pie chart di atas menunjukkan distribusi 10 publisher teratas dari total 16.807 penerbit dalam dataset. Harlequin menempati posisi teratas dengan proporsi s![Screenshot 2025-05-29 081840](https://github.com/user-attachments/assets/f522f633-dfa9-4a49-a3d2-b19ee7bc5561)
ekitar 20,1%, disusul oleh Silhouette, Pocket, Ballantine Books, dan lainnya. Tingginya popularitas publisher ini dapat digunakan sebagai salah satu fitur dalam sistem rekomendasi, khususnya pada pendekatan content-based filtering, untuk mengidentifikasi kecenderungan pengguna terhadap penerbit tertentu.

#### EDA pada data ratings
![Output dari ratings.info](https://github.com/user-attachments/assets/14dae14c-1f4a-4f40-9741-04c5aaf3c92a)

Dapat dilihat dari gambar diatas, data rating pada dataset ini terdiri dari 1.149.780 jumlah baris data, dan 3 kolom yang terbagi menjadi 2 kolom numerik (int64), dan 1 kolom bertipe data object. Setelah itu, ditampilkan seluruh jumlah kolom uniknya, meliputi 105.283 user unik yang memberikan rating, 340556 buku unik yang direview, dan jumlah ratingnya adalah 1.149.780 penilaian.

![Output dari ratings.describe](https://github.com/user-attachments/assets/cddc8cbf-4939-4c76-8570-3fa4ecaf52f5)

Statistik deskriptif pada gambar menunjukkan bahwa dari 1.149.780 data rating, sebagian besar pengguna memberikan nilai 0, yang terlihat dari median (50%) dan kuartil pertama (25%) yang juga bernilai 0, serta rata-rata rating hanya sebesar 2.87. Hal ini mengindikasikan bahwa banyak pengguna tidak memberikan rating eksplisit atau kemungkinan rating 0 merepresentasikan ketidaktertarikan. Sementara itu, nilai maksimum rating mencapai 10, yang menunjukkan adanya pengguna yang memberikan penilaian tinggi terhadap buku yang mereka sukai.

#### EDA pada data users
![Output dari users.info](https://github.com/user-attachments/assets/7f72fe67-c397-4e86-9560-2f7212d2e7e6)

Dapat dilihat dari gambar diatas, data pengguna pada dataset ini terdiri dari 278.858 jumlah baris data, dan 3 kolom yang terbagi menjadi 2 kolom numerik (int64 dan float64), dan 1 kolom bertipe data object. Kolom Age menunjukkan adanya banyak nilai yang hilang (missing value) atau kosong (null), yang perlu diperhatikan dalam tahap pembersihan data karena dapat memengaruhi kualitas analisis dan hasil sistem rekomendasi.

![Output dari users.head](https://github.com/user-attachments/assets/d2a23094-fafb-4d1a-bfeb-7d3ef6a45365)
Terlihat dari gambar diatas, data pengguna terdiri dari lokasi, dan umur pengguna yang sebenarnya berguna untuk membangun sistem rekomendasi berbasis demografi yang lebih kompleks. Namun, dalam studi kasus ini, sistem rekomendasi content-based filtering hanya akan menggunakan informasi dari item buku saja, seperti judul, author (penulis), dan publisher(penerbit). Selain itu, alasannya juga karena banyaknya missing value pada age juga menjadi pertimbangan pemilihan fitur.


## Data Preparation
Tahap data preparation merupakan proses penting sebelum membangun model machine learning, termasuk sistem rekomendasi. Dalam studi kasus ini, data perlu disiapkan agar sesuai dengan kebutuhan dua pendekatan yang digunakan, yaitu content-based filtering dan collaborative filtering. Masing-masing pendekatan memiliki karakteristik dan kebutuhan data yang berbeda, sehingga proses persiapan dilakukan secara terpisah untuk memastikan setiap model dapat bekerja secara optimal.

Selanjutnya, bagian ini akan menguraikan tahapan-tahapan data preparation untuk masing-masing pendekatan secara terstruktur.

### Preparation Data Content-Based Filtering
- Menggabungkan data dari rating dengan yang ada pada data buku (ISBN, judul, author, dan publisher saja) menggunakan metode left join. Penggabungan data dari tabel rating dan data buku menggunakan left join diperlukan agar setiap entri rating memiliki informasi tambahan seperti judul, author, dan publisher yang dibutuhkan dalam content-based filtering.

- Menangani missing value. Terlihat ada beberapa missing value di kolom seperti judul, author, dan publisher. Missing value yang muncul merupakan hal wajar karena proses penggabungan dilakukan dengan left join, sehingga tidak semua ISBN pada rating memiliki data lengkap di tabel buku. Penanganan missing value dilakukan untuk menghindari error saat pemrosesan dan pelatihan model, serta menjaga kualitas fitur yang digunakan.

- Menangani data duplikat pada variabel ISBN. Penghapusan data duplikat berdasarkan ISBN dilakukan untuk memastikan bahwa setiap buku hanya direpresentasikan satu kali dalam pemodelan content-based filtering. Duplikasi dapat menyebabkan hasil perhitungan kesamaan antar buku menjadi tidak konsisten dan menimbulkan bias, sehingga proses ini penting agar sistem rekomendasi menghasilkan saran yang lebih relevan dan akurat.

- Mengambil hanya 10000 data acak. Pengambilan sampel 10.000 data acak dilakukan untuk mempercepat proses pelatihan model selama tahap pengembangan awal karena keterbatasan sumber daya pengolahan data serta demi efisiensi proses pelatihan model tanpa mengorbankan keragaman sampel.

- Membuat dictionary. Pembuatan dictionary atau kamus ID item seperti author atau publisher berguna untuk memetakan fitur tekstual menjadi representasi yang bisa digunakan dalam model berbasis konten (misalnya TF-IDF atau one-hot encoding).

### Preparation Data Collaborative Filtering
- Pengambilan sampel data rating buku secara acak 10.000 data. Hal ini dilakukan dengan menetapkan nilai random state untuk menjaga konsistensi hasil. Langkah ini bertujuan membatasi ruang lingkup data agar proses pelatihan model deep learning tetap efisien dan tidak memerlukan sumber daya komputasi yang terlalu besar.

- Label encoding untuk ID pengguna (User-ID) dan buku (ISBN). Label encoding terhadap User-ID dan ISBN diperlukan karena model neural network tidak bisa bekerja langsung dengan string, semua ID perlu dikonversi ke format numerik.

- Mapping id yang telah diencode. Setelah dilakukan encoding, dibuat juga pemetaan balik (mapping) antara ID yang telah diencode dengan ID aslinya. Mapping ini sangat berguna pada tahap prediksi, agar sistem dapat mengembalikan hasil prediksi rekomendasi dalam format yang dimengerti pengguna, seperti menampilkan nama pengguna atau judul buku yang sebenarnya, bukan dalam bentuk angka hasil encoding.

- Mengonversi tipe data rating ke float. Rating dikonversi ke tipe data float untuk memastikan konsistensi format numerik yang sesuai dengan kebutuhan model. Format float diperlukan agar model dapat melakukan operasi matematis secara presisi selama proses pelatihan. 

- Normalisasi rating dan Split Data. Normalisasi rating ke skala 0–1 bertujuan agar model dapat belajar lebih stabil, menghindari bias akibat perbedaan skala rating. Sementara itu, pembagian data menjadi 80% data latih dan 20% data validasi dilakukan untuk mengukur performa model secara objektif dan mencegah overfitting. Dengan cara ini, kita dapat mengevaluasi seberapa baik model bekerja pada data yang belum pernah dilihat sebelumnya.


## Modeling
Pada tahap ini, dilakukan pembangunan dan evaluasi dua model sistem rekomendasi yang bertujuan untuk membantu pengguna menemukan buku yang sesuai dengan preferensi mereka. Model ini dikembangkan berdasarkan dua pendekatan utama, yaitu content-based filtering dan collaborative filtering berbasis neural network. Pendekatan pertama fokus pada konten buku itu sendiri, seperti judul, penulis, dan penerbit, serta memanfaatkan kesamaan antar item untuk menghasilkan rekomendasi. Pendekatan kedua memanfaatkan pola interaksi antar pengguna dan item melalui data rating historis yang tersedia, dan menggunakan pembelajaran mesin untuk memprediksi ketertarikan pengguna terhadap buku-buku yang belum mereka baca. Berikut penjelasan masing-masing algoritma:

### Content-Based Filtering
Pada pendekatan content-based filtering, sistem rekomendasi dikembangkan dengan menganalisis kemiripan antar buku berdasarkan karakteristik kontennya, seperti judul, nama penulis (author), dan penerbit (publisher). Proses ini dimulai dengan menerapkan teknik TF-IDF vectorization pada fitur-fitur teks tersebut untuk mengubahnya menjadi representasi numerik. Selanjutnya, digunakan metode cosine similarity untuk menghitung sejauh mana kesamaan antar buku. Berdasarkan hasil perhitungan tersebut, sistem akan mengidentifikasi buku-buku yang memiliki tingkat kemiripan tinggi dengan buku yang sebelumnya telah diberi rating tinggi oleh pengguna. Dari sana, sistem akan memberikan rekomendasi berupa daftar Top-N buku yang paling relevan dan serupa dengan preferensi pengguna.
- Kelebihan: 
    - Tidak memerlukan data dari pengguna lain (independen).
    - Dapat memberikan rekomendasi meskipun data pengguna masih sedikit (cold start user).

- Kekurangan:
    - Tidak bisa merekomendasikan buku yang jauh berbeda dari histori pengguna.
    - Kualitas rekomendasi terbatas pada informasi konten buku saja.

### Collaborative Filtering
Pendekatan kedua menggunakan collaborative filtering berbasis neural network, yang berfokus pada pola interaksi antara pengguna dan buku melalui data rating. Model ini memanfaatkan embedding layer untuk mengubah ID pengguna dan ID buku menjadi representasi vektor di ruang laten, sehingga hubungan antar entitas dapat dipelajari secara lebih mendalam. Selama proses pelatihan, model belajar memprediksi rating yang mungkin diberikan pengguna terhadap buku tertentu. Setelah pelatihan selesai, sistem dapat menghasilkan rekomendasi dengan memprediksi rating untuk seluruh buku yang belum pernah dibaca oleh pengguna, kemudian memilih N buku dengan prediksi tertinggi sebagai rekomendasi personal.
- Kelebihan:
    - Memanfaatkan pola interaksi antar pengguna, sehingga dapat menemukan hubungan yang tidak terlihat dari konten buku saja.
    - Cocok untuk memberikan rekomendasi personal berdasarkan histori komunitas pengguna.

- Kekurangan:
    - Membutuhkan data interaksi pengguna yang cukup besar untuk bekerja optimal.
    - Sangat sulit bahkan tidak bisa merekomendasikan buku untuk pengguna baru (cold start problem).


## Evaluation
Dalam proyek ini, digunakan dua pendekatan utama untuk sistem rekomendasi, yaitu content-based filtering dan collaborative filtering. Masing-masing pendekatan dievaluasi menggunakan metrik yang sesuai dengan karakteristik metode dan tujuan bisnis yang ingin dicapai.

### Metrik Evaluasi Content-Based Filtering : Recall@K
Untuk mengevaluasi performa model content-based filtering, digunakan metrik Recall@K. Metrik ini mengukur seberapa banyak item relevan yang berhasil direkomendasikan kepada pengguna dalam daftar rekomendasi sebanyak K item.Secara matematis, Recall@K dirumuskan sebagai:

$$
\text{Recall@K} = \frac{\text{Jumlah item relevan dalam top-K}}{\text{Total item relevan untuk pengguna}}
$$

Metrik ini bekerja dengan cara menghitung proporsi item yang benar-benar relevan bagi pengguna yang muncul dalam daftar rekomendasi sebanyak K item. Semakin tinggi nilai Recall@K, semakin baik sistem dalam menemukan item yang sesuai dengan preferensi pengguna. 

Hasil : 
| No | Judul Buku                                              | Author              | Publisher           | Relevan? |
|----|----------------------------------------------------------|---------------------|---------------------|----------|
| 1  | A Man of Affairs (Signet Regency Romance)                | Anne Barbour        | Signet Book         | ✅       |
| 2  | The Nobody (Signet Regency Romance)                      | Diane Farr          | Signet Book         | ✅       |
| 3  | George Bellows: American Artist (Writers on Art)         | Joyce Carol Oates   | Harpercollins       | ❌       |
| 4  | The Runaways (Signet Regency Romance)                    | Barbara Hazard      | Signet Book         | ✅       |
| 5  | Dubliners                                                | James Joyce         | Signet Classics     | ✅       |
| 6  | Dubliners (Essential.penguin S.)                         | James Joyce         | Penguin Books Ltd   | ✅       |
| 7  | A London Season (Signet Regency Romance)                 | Joan Wolf           | Signet Book         | ✅       |
| 8  | Impostress (Signet Historical Romance)                   | Lisa Jackson        | Signet Book         | ✅       |
| 9  | Arabia-Una nubecilla-Duplicados                          | James Joyce         | Sudamericana        | ✅       |
|10  | Gentleman Jack (Signet Regency Romance)                  | Margaret Summerville| Signet Book         | ✅       |

$$
Recall@10 = \frac{9}{10} = 0.90
$$

Dengan nilai Recall@10 = 0.90, dapat disimpulkan bahwa sistem mampu merekomendasikan buku-buku yang relevan dengan baik dalam 10 besar hasil.

### Metrik Evaluasi Collaborative Filtering : RMSE (Root Mean Squared Error)
Untuk pendekatan collaborative filtering, digunakan metrik Root Mean Square Error (RMSE). RMSE mengukur deviasi rata-rata antara rating yang diprediksi sistem dan rating aktual dari pengguna. Rumus RMSE dituliskan sebagai:

$$
\text{RMSE} = \sqrt{\frac{1}{N} \sum_{i=1}^{N} (r_i - \hat{r}_i)^2}
$$

di mana 𝑟𝑖 adalah rating aktual dan 𝑟^𝑖 adalah rating prediksi. Metrik ini bekerja dengan mengukur selisih kuadrat antara prediksi dan nilai sebenarnya, kemudian mengambil akar dari rata-ratanya. Semakin kecil nilai RMSE, semakin akurat prediksi yang dihasilkan oleh sistem.

Hasil : 
![Evaluasi RMSE Collaborative Filtering](https://github.com/user-attachments/assets/a91c9e0e-45f2-42db-9195-580bf4e7d52a)

Berdasarkan grafik di atas, nilai RMSE pada data latih terus menurun seiring bertambahnya epoch, yang menandakan bahwa model berhasil mempelajari pola dari data dengan baik. Namun, RMSE pada data uji mulai mendatar dan sedikit meningkat setelah sekitar 20 epoch. Hal ini mengindikasikan adanya gejala overfitting, di mana model terlalu menyesuaikan diri terhadap data latih sehingga performanya pada data baru mulai menurun. Meskipun demikian, nilai RMSE pada data uji tetap berada pada kisaran rendah (sekitar 0.40), yang menunjukkan bahwa model memiliki performa prediksi yang cukup baik secara keseluruhan.

### Kesimpulan
Berdasarkan hasil yang diperoleh dari proyek ini, sistem rekomendasi buku berhasil dibangun dengan dua pendekatan utama, yaitu content-based filtering dan collaborative filtering berbasis neural network, yang masing-masing menjawab kebutuhan dari dua problem statement yang telah dirumuskan sebelumnya.

Untuk problem pertama, yaitu bagaimana membangun sistem rekomendasi berdasarkan kesamaan konten buku, pendekatan content-based filtering terbukti efektif dalam memberikan rekomendasi yang relevan. Dengan memanfaatkan fitur-fitur metadata seperti judul, penulis, dan penerbit, serta menggunakan representasi berbasis TF-IDF dan cosine similarity, sistem mampu menghasilkan rekomendasi buku yang memiliki kemiripan tinggi dengan preferensi pengguna sebelumnya. Hasil evaluasi menggunakan metrik Recall@10 menunjukkan nilai sebesar 0.90, yang berarti 90% dari buku-buku yang relevan berhasil muncul dalam 10 rekomendasi teratas. Hal ini mengindikasikan bahwa sistem berhasil menyediakan rekomendasi yang dipersonalisasi berbasis konten, sehingga goal pertama tercapai.

Untuk problem kedua, yaitu bagaimana merekomendasikan buku yang belum pernah dibaca oleh pengguna dengan memanfaatkan histori rating dari pengguna lain, pendekatan collaborative filtering berbasis neural network menunjukkan performa yang cukup baik. Dengan menggunakan embedding untuk mempelajari representasi pengguna dan buku dari interaksi rating, sistem dapat memprediksi kemungkinan ketertarikan pengguna terhadap buku yang belum pernah mereka baca. Evaluasi menggunakan metrik Root Mean Squared Error (RMSE) menghasilkan nilai sekitar 0.40, yang menunjukkan bahwa prediksi sistem terhadap rating cukup akurat dan mendekati nilai aktual. Dengan demikian, goal kedua juga berhasil dicapai, yaitu membantu pengguna menemukan buku-buku baru yang relevan melalui preferensi kolektif pengguna lain.

Secara keseluruhan, solusi yang diterapkan berdampak positif terhadap permasalahan yang dihadapi. Pendekatan content-based membantu mempertahankan relevansi berdasarkan histori pengguna, sementara pendekatan collaborative filtering memungkinkan sistem memberikan rekomendasi yang lebih variatif dan menjangkau buku-buku yang belum diketahui pengguna. Kombinasi kedua pendekatan ini memperkuat personalisasi dan meningkatkan kualitas sistem rekomendasi secara umum, sesuai dengan tujuan proyek ini.

## Referensi

[1] F. Ricci, L. Rokach, B. Shapira, and P. B. Kantor, *Recommender Systems Handbook*, Springer, 2011.

[2] U. Ungkawa, D. Rosmala, dan F. Aryanti, “Pembangunan Aplikasi Travel Recommender dengan Metode Case Base Reasoning,” *Jurnal Informatika*, vol. 4, no. 1, pp. 57–68, 2011.

[3] S. R. S. Reddy et al., “Content-Based Movie Recommendation System Using Genre Correlation,” in *Smart Intelligent Computing and Applications*, S. C. Satapathy, V. Bhateja, and S. Das, Eds. Singapore: Springer, 2019, pp. 391–397.

[4] H. Februaryianti, A. D. Laksono, J. S. Wibowo, dan M. S. Utomo, “Implementasi Metode Collaborative Filtering untuk Sistem Rekomendasi Penjualan pada Toko Mebel,” *Jurnal Khatulistiwa Informatika*, vol. 9, no. 1, pp. 43–50, Jun. 2021.

[5] A. Zadeh, “Book Recommendation Dataset,” Kaggle, 2020. [Online]. Available: https://www.kaggle.com/datasets/arashnic/book-recommendation-dataset

**---Ini adalah bagian akhir laporan---**


