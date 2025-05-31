# **LAPORAN PROYEK SISTEM REKOMENDASI BUKU**

## **1. Project Overview (Ulasan Proyek)**

Proyek ini bertujuan untuk mengembangkan sebuah **sistem rekomendasi buku** menggunakan pendekatan *content-based filtering*. Di era digital saat ini, di mana jumlah buku yang tersedia sangatlah masif baik dalam format fisik maupun digital, para pembaca seringkali kesulitan dalam menemukan buku yang sesuai dengan minat dan preferensi mereka. Permasalahan ini menjadi semakin signifikan dengan adanya platform seperti Goodreads yang menyediakan jutaan judul buku, rating, dan ulasan. Tanpa sistem yang cerdas, pembaca mungkin akan melewatkan buku-buku bagus yang sebenarnya sangat relevan bagi mereka, atau terjebak dalam rekomendasi yang kurang personal.

Sistem rekomendasi menjadi solusi krusial untuk mengatasi *information overload* ini. Dengan menganalisis karakteristik buku yang telah disukai pengguna (atau dalam kasus ini, karakteristik buku itu sendiri), sistem dapat menyarankan item-item serupa, meningkatkan pengalaman pengguna, dan membantu mereka menemukan judul-judul baru yang relevan. Proyek ini berfokus pada pendekatan *content-based filtering* karena memungkinkan rekomendasi yang sangat personal tanpa memerlukan data historis interaksi pengguna dalam jumlah besar, cukup dengan informasi detail dari setiap buku.

-----

## **2. Business Understanding**

### **Problem Statements (Pernyataan Masalah)**

1.  **Kesulitan Pencarian Buku:** Pembaca seringkali kesulitan menemukan buku baru yang sesuai dengan minat dan preferensi mereka di tengah lautan judul yang sangat banyak.
2.  **Keterbatasan Rekomendasi Konvensional:** Metode pencarian buku konvensional (misalnya, berdasarkan *bestseller* atau kategori umum) seringkali tidak cukup personal dan relevan untuk setiap individu.
3.  **Memaksimalkan Penemuan Konten:** Bagaimana cara membantu pembaca menemukan buku-buku yang relevan dengan cepat dan efisien, sehingga meningkatkan kepuasan membaca mereka?

### **Goals (Tujuan)**

Tujuan utama dari proyek ini adalah **membangun sebuah sistem rekomendasi buku yang mampu menyarankan buku-buku lain yang memiliki karakteristik konten serupa dengan buku yang diminati pengguna.** Sistem ini diharapkan dapat membantu pembaca dalam eksplorasi buku dan memperkaya pengalaman membaca mereka.

### **Solution Approach**

Untuk mencapai tujuan tersebut, kami mempertimbangkan dua pendekatan utama dalam sistem rekomendasi:

1.  **Content-Based Filtering:** Pendekatan ini merekomendasikan item yang serupa dengan item yang disukai pengguna di masa lalu. Dalam konteks buku, ini berarti merekomendasikan buku-buku yang memiliki karakteristik (judul, penulis, bahasa, rating, dll.) yang mirip dengan buku yang dipilih. Keuntungan utama dari metode ini adalah kemampuannya untuk merekomendasikan item-item baru yang belum pernah dilihat pengguna (masalah *cold-start* untuk item), dan rekomendasi yang sangat personal. Proyek ini akan **mengimplementasikan pendekatan *content-based filtering*** karena kesesuaiannya dengan dataset yang tersedia yang kaya akan informasi atribut buku.
2.  **Collaborative Filtering:** Pendekatan ini merekomendasikan item berdasarkan preferensi pengguna yang mirip. Misalnya, jika pengguna A menyukai buku X dan Y, dan pengguna B juga menyukai buku X, maka sistem dapat merekomendasikan buku Y kepada pengguna B. Keuntungan utamanya adalah kemampuannya menemukan pola yang kompleks dalam preferensi pengguna. Namun, metode ini membutuhkan data interaksi pengguna yang ekstensif (rating, pembelian, dll.) dan dapat menghadapi masalah *cold-start* bagi pengguna baru.

Mempertimbangkan dataset yang tersedia yang berfokus pada atribut buku, serta tujuan untuk menyarankan buku berdasarkan kesamaan konten, pendekatan **Content-Based Filtering** adalah pilihan yang paling sesuai dan akan menjadi fokus implementasi dalam proyek ini.

-----

## **3. Data Understanding**

Dataset yang digunakan dalam proyek ini adalah **Goodreads Books** yang diperoleh dari Kaggle, dapat diakses melalui tautan berikut: [https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks](https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks).

Dataset ini mengandung informasi lengkap mengenai **11.119 buku** dan memiliki 23 kolom. Setelah memuat dataset ke dalam *dataframe* dan membersihkan nama kolom dari spasi, berikut adalah informasi mengenai jumlah data dan variabel yang tersedia:


**Kondisi Data Awal:**

  * **Jumlah Data:** 11.119 baris dan 23 kolom.
  * **Missing Values:** Tidak ada *missing values* terdeteksi pada kolom-kolom kunci yang akan digunakan setelah pembersihan awal.
  * **Data Duplikat:** Tidak ada data duplikat yang terdeteksi.

**Variabel/Fitur pada Dataset:**

  * `bookID`: ID unik untuk setiap buku.
  * `title`: Judul buku.
  * `authors`: Nama penulis buku.
  * `average_rating`: Rata-rata rating pengguna untuk buku.
  * `isbn` dan `isbn13`: Kode identifikasi buku versi 10 dan 13 digit.
  * `language_code`: Kode bahasa buku (misalnya, 'eng', 'fre', 'ger').
  * `num_pages`: Jumlah halaman buku.
  * `ratings_count`: Total jumlah rating yang diterima buku.
  * `text_reviews_count`: Total jumlah ulasan teks dari pengguna.
  * `publication_date`: Tanggal terbit buku.
  * `publisher`: Nama penerbit.

-----

### **Exploratory Data Analysis (EDA)**

EDA dilakukan untuk memahami distribusi dan karakteristik dari setiap variabel penting dalam dataset, serta untuk mendapatkan **insight mendalam** yang akan memandu proses pra-pemrosesan dan pengembangan model.

#### **Eksplorasi Variabel `title` dan `authors`**

Dataset ini sangat kaya dengan **10.849 judul buku unik** dan **6.643 penulis yang berbeda**, menunjukkan keragaman konten yang tinggi. Penulis seperti **Margaret Atwood, Stephen King, dan James Patterson** memiliki kontribusi buku terbanyak, mengindikasikan bahwa karya-karya mereka mungkin memiliki pengaruh signifikan dalam basis data buku ini dan bisa menjadi titik fokus rekomendasi jika pengguna menyukai gaya penulis tertentu.

#### **Eksplorasi Variabel `average_rating`**

Distribusi *average\_rating* menunjukkan bahwa **mayoritas buku dalam dataset memiliki rata-rata rating di atas 3.5**, dengan puncak yang signifikan di sekitar 4.0. Hal ini menandakan dataset cenderung berisi buku-buku yang umumnya disukai oleh pembaca, yang bisa menjadi keuntungan dalam merekomendasikan buku-buku berkualitas tinggi. Namun, perlu diperhatikan bahwa ada juga buku dengan rating sangat rendah (mendekati 0), yang perlu ditangani untuk menghindari rekomendasi buku berkualitas rendah.

#### **Eksplorasi Variabel `language_code`**

Terlihat jelas bahwa **bahasa Inggris ('eng' dan 'en') mendominasi dataset**, dengan proporsi yang sangat besar dibandingkan bahasa lain. Hal ini **sangat informatif** karena menunjukkan bahwa sistem rekomendasi paling relevan untuk pengguna berbahasa Inggris. Ini juga menjadi dasar kuat untuk melakukan pemfilteran data dan hanya mempertahankan buku berbahasa Inggris, agar rekomendasi tidak tercampur dengan buku dari bahasa yang tidak dipahami oleh sebagian besar target pengguna.

#### **Eksplorasi Variabel `text_reviews_count` dan `ratings_count`**

Kedua variabel ini menunjukkan **distribusi yang sangat condong ke kanan (right-skewed)**, yang berarti sebagian besar buku memiliki jumlah ulasan dan rating yang relatif sedikit, sementara hanya segelintir buku yang sangat populer memiliki jumlah ulasan/rating yang sangat tinggi. Pola ini umum dalam dataset populer, menunjukkan adanya **outlier** pada buku-buku yang sangat populer. Transformasi logaritmik (`np.log1p`) sangat membantu dalam memvisualisasikan distribusi ini dengan lebih baik, namun **ekstremitas pada ujung kanan (outlier) menunjukkan potensi bias** jika data ini digunakan tanpa penyesuaian, karena buku-buku yang sangat populer mungkin akan lebih sering muncul dalam rekomendasi meskipun kontennya tidak sepenuhnya relevan.

#### **Eksplorasi Variabel `num_pages`**

Mayoritas buku memiliki jumlah halaman antara 200 hingga 400. Meskipun tidak langsung digunakan sebagai fitur konten, informasi ini dapat berguna untuk memfilter buku dengan jumlah halaman yang tidak realistis atau untuk mengidentifikasi buku-buku yang sangat tipis/tebal jika diperlukan dalam kasus penggunaan tertentu.

#### **Eksplorasi Variabel `publisher`**

Publishers seperti **Vintage, Penguin Books, dan Bantam Dell Publishing Group** menerbitkan jumlah buku terbanyak dalam dataset ini. Informasi ini tidak secara langsung menjadi fitur dalam model *content-based filtering* ini, namun dapat memberikan gambaran tentang dominasi penerbit tertentu dan potensi bias koleksi buku dalam dataset.

-----

## **4. Data Preparation**

Tahap persiapan data sangat penting untuk memastikan data dalam format yang tepat dan bersih untuk pembangunan model. Proses ini melibatkan pemilihan fitur, pemfilteran data, dan penggabungan fitur. Setiap langkah dijelaskan dengan tujuannya.

### **Teknik Data Preparation yang Dilakukan:**

1.  **Penghapusan Kolom Tidak Relevan:**

      * **Teknik:** Menghapus kolom `bookID`, `isbn`, `isbn13`, `num_pages`, `publication_date`, dan `publisher`.
      * **Tujuan:** Kolom-kolom ini dihapus karena **tidak mengandung informasi relevan** yang secara langsung berkontribusi pada kesamaan konten dalam sistem rekomendasi *content-based*. Misalnya, `bookID` atau `isbn` adalah identifikasi unik buku, bukan deskripsi konten. `num_pages`, `publication_date`, dan `publisher` meskipun merupakan atribut buku, tidak digunakan dalam definisi "konten" untuk sistem rekomendasi ini dan **hanya akan membebani model dengan variabel yang tidak berguna** atau mengurangi fokus pada karakteristik utama seperti judul, penulis, rating, dan ulasan teks.
      * **Implementasi:**
        ```python
        df.drop(columns=['bookID', 'isbn', 'isbn13','num_pages', 'publication_date','publisher'], inplace=True)
        ```

2.  **Pemfilteran Buku Berdasarkan Bahasa:**

      * **Teknik:** Mempertahankan hanya buku dengan `language_code` 'en' atau 'eng'.
      * **Tujuan:** Seperti yang terlihat pada EDA, bahasa Inggris sangat mendominasi dataset. Pemfilteran ini dilakukan untuk **memastikan relevansi rekomendasi bagi pengguna berbahasa Inggris**, yang merupakan target utama dari sistem ini. Dengan fokus pada satu bahasa, kita **menghindari kompleksitas dan potensi *noise*** dari buku-buku dengan bahasa yang berbeda, yang mungkin tidak dipahami oleh mayoritas pengguna, sehingga **meningkatkan kualitas dan koherensi rekomendasi**.
      * **Implementasi:**
        ```python
        df = df[df['language_code'].isin(['en', 'eng'])]
        ```

3.  **Penanganan Anomali `average_rating`:**

      * **Teknik:** Memfilter buku dengan `average_rating` antara nilai valid (\>0 dan \<=5).
      * **Tujuan:** Meskipun EDA menunjukkan distribusi rating yang baik, beberapa entri mungkin memiliki nilai `average_rating` yang tidak logis (misalnya 0, atau nilai di luar rentang standar 1-5 yang umum digunakan pada platform rating). Pemfilteran ini dilakukan untuk **memastikan bahwa data rating yang digunakan valid dan konsisten**, sehingga **tidak menimbulkan bias atau kesalahan** dalam perhitungan kesamaan konten yang menggunakan rating sebagai salah satu fitur.
      * **Implementasi:**
        ```python
        df = df[(df['average_rating'] > 0) & (df['average_rating'] <= 5)]
        ```

4.  **Pembentukan `combined_features`:**

      * **Teknik:** Menggabungkan kolom `title`, `authors`, `language_code`, `average_rating`, `ratings_count`, dan `text_reviews_count` menjadi satu kolom teks baru bernama `combined_features`. Kolom numerik dikonversi ke string sebelum digabungkan.
      * **Tujuan:** Langkah ini **sangat krusial** karena `TF-IDF Vectorizer` hanya dapat memproses data dalam format teks. Dengan menggabungkan semua fitur yang relevan menjadi satu string, kita **membuat representasi komprehensif** dari setiap buku yang akan menjadi dasar bagi *TF-IDF* untuk mengekstrak kata kunci dan konsep. Ini memungkinkan *TF-IDF* untuk menangkap kesamaan tidak hanya dari judul dan penulis, tetapi juga dari indikator popularitas dan kualitas (rating, jumlah ulasan) dan bahasa, yang semuanya berkontribusi pada definisi "konten" dalam konteks ini.
      * **Implementasi:**
        ```python
        df['combined_features'] = (
            df['title'] + ' ' +
            df['authors'] + ' ' +
            df['language_code'] + ' ' +
            df['average_rating'].astype(str) + ' ' +
            df['ratings_count'].astype(str) + ' ' +
            df['text_reviews_count'].astype(str)
        )
        ```

Proses *data preparation* ini memastikan bahwa data yang masuk ke model adalah data yang relevan, bersih, dan dalam format yang sesuai untuk ekstraksi fitur berbasis teks, yang esensial untuk kinerja optimal sistem rekomendasi *content-based*.

-----

## **5. Modeling and Result**

### **Membangun Sistem Rekomendasi Content-Based Filtering**

Sistem rekomendasi ini dibangun menggunakan pendekatan *content-based filtering* dengan memanfaatkan teknik *TF-IDF Vectorization* dan *Cosine Similarity*.

1.  **TF-IDF Vectorization:**

      * **Penjelasan:** *TF-IDF (Term Frequency-Inverse Document Frequency)* adalah teknik untuk mengubah koleksi dokumen teks (dalam kasus ini, `combined_features` dari setiap buku) menjadi representasi numerik (vektor fitur). Bobot TF-IDF mencerminkan seberapa penting sebuah kata dalam sebuah dokumen relatif terhadap koleksi dokumen keseluruhan. Kata-kata umum (seperti "the", "a", "is") akan memiliki bobot rendah, sementara kata-kata unik dan deskriptif akan memiliki bobot tinggi. `stop_words='english'` digunakan untuk menghapus kata-kata umum yang tidak memberikan informasi berarti, **bertujuan untuk meningkatkan relevansi** kata kunci yang tersisa dalam menentukan kesamaan antar buku.
      * **Implementasi:**
        ```python
        from sklearn.feature_extraction.text import TfidfVectorizer
        vectorizer = TfidfVectorizer(stop_words='english')
        tfidf_matrix = vectorizer.fit_transform(df['combined_features'])
        print("Shape TF-IDF matrix:", tfidf_matrix.shape)
        ```
        `tfidf_matrix` sekarang berisi representasi numerik dari setiap buku, di mana setiap baris merepresentasikan sebuah buku dan setiap kolom merepresentasikan sebuah kata dengan bobot TF-IDF-nya.

2.  **Menghitung Cosine Similarity:**

      * **Penjelasan:** *Cosine Similarity* adalah metrik yang mengukur kemiripan antara dua vektor non-nol dalam ruang vektor. Semakin dekat nilai *cosine similarity* ke 1, semakin mirip kedua item. Dalam konteks ini, kita menghitung *cosine similarity* antara setiap pasang buku berdasarkan vektor TF-IDF mereka. `linear_kernel` digunakan karena lebih cepat untuk menghitung *cosine similarity* ketika vektor sudah dinormalisasi (seperti hasil dari TF-IDF), **bertujuan untuk efisiensi komputasi** pada matriks yang besar.
      * **Implementasi:**
        ```python
        from sklearn.metrics.pairwise import linear_kernel
        cosine_sim = linear_kernel(tfidf_matrix, tfidf_matrix)
        print("Shape Cosine Similarity matrix:", cosine_sim.shape)
        ```
        `cosine_sim` adalah matriks simetri yang menunjukkan tingkat kemiripan antara setiap buku dengan buku lainnya, menjadi inti dari mekanisme rekomendasi kita.

### **Fungsi Rekomendasi**

Fungsi `recommend_books` dibuat untuk mengambil judul buku sebagai input dan mengembalikan 10 buku teratas yang paling mirip berdasarkan skor *cosine similarity*.

```python
def recommend_books(title, cosine_sim=cosine_sim):
    # Mengambil indeks buku berdasarkan judul
    idx = df[df['title'].str.lower() == title.lower()].index

    # Menangani kasus buku tidak ditemukan
    if len(idx) == 0:
        return "Buku tidak ditemukan dalam dataset."

    idx = idx[0] # Ambil indeks pertama jika ada duplikat judul

    # Dapatkan skor kesamaan dari buku tersebut dengan semua buku lain
    sim_scores = list(enumerate(cosine_sim[idx]))

    # Urutkan buku berdasarkan skor kesamaan secara menurun
    sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)

    # Ambil 10 buku teratas (mengabaikan buku itu sendiri)
    sim_scores = sim_scores[1:11] # [1:11] untuk mengambil 10 rekomendasi, melewatkan buku itu sendiri

    # Dapatkan indeks buku-buku yang direkomendasikan
    book_indices = [i[0] for i in sim_scores]

    # Kembalikan judul dan penulis dari buku-buku yang direkomendasikan
    return df[['title', 'authors']].iloc[book_indices]
```

### **Contoh Penggunaan Model (Top-N Recommendation)**

Berikut adalah contoh rekomendasi untuk buku 'The Hobbit':

```python
print("\nContent-Based Recommendations untuk 'The Hobbit':")
print(recommend_books('The Hobbit'))
```

**Output:**

```
Content-Based Recommendations untuk 'The Hobbit':
                          title                       authors
10332  The Fellowship of the Ring        J.R.R. Tolkien, Alan Lee
10331    The Two Towers (The Lord of the Rings  #2)  J.R.R. Tolkien, Alan Lee
10330   The Return of the King (The Lord of the Rings  #3) J.R.R. Tolkien, Alan Lee
10334  The Lord of the Rings  J.R.R. Tolkien
10333  The Silmarillion      J.R.R. Tolkien, Christopher Tolkien
10335  Unfinished Tales       J.R.R. Tolkien, Christopher Tolkien
10336  The Children of Húrin J.R.R. Tolkien, Christopher Tolkien
10337  The History of Middle-Earth (The History of Middle-Earth  #1-12) J.R.R. Tolkien, Christopher Tolkien
10338  The Book of Lost Tales  Part One (The History of Middle-Earth  #1) J.R.R. Tolkien, Christopher Tolkien
10339  The Book of Lost Tales  Part Two (The History of Middle-Earth  #2) J.R.R. Tolkien, Christopher Tolkien
```

Hasil rekomendasi untuk 'The Hobbit' menunjukkan buku-buku lain yang ditulis oleh J.R.R. Tolkien, terutama yang berkaitan dengan dunia Middle-earth. Ini adalah hasil yang **sangat relevan dan sesuai dengan ekspektasi** dari sistem *content-based filtering*, karena buku-buku tersebut memiliki kesamaan konten yang jelas berdasarkan penulis, judul, dan secara implisit, genre fantasi.

### **Kelebihan dan Kekurangan Pendekatan Content-Based Filtering:**

**Kelebihan:**

  * **Personalisasi Tinggi:** Model ini mampu merekomendasikan item yang sangat spesifik dan relevan dengan profil preferensi pengguna (atau dalam kasus ini, item input) karena fokus pada atribut konten.
  * **Mengatasi Masalah *Cold-Start* Item Baru:** Sistem dapat merekomendasikan item baru segera setelah item tersebut ditambahkan ke katalog, asalkan atribut kontennya tersedia. Ini **tidak memerlukan data interaksi pengguna** sebelumnya, menjadikannya solusi yang baik untuk katalog yang terus berkembang.
  * **Transparansi Rekomendasi:** Alasan di balik suatu rekomendasi mudah dijelaskan (misalnya, "Anda menyukai buku ini karena mirip dengan buku X yang memiliki penulis dan tema yang sama"). Ini **meningkatkan kepercayaan pengguna** terhadap sistem.

**Kekurangan:**

  * **Keterbatasan Penemuan :** Sistem cenderung merekomendasikan item yang sangat mirip dengan apa yang sudah diketahui pengguna atau item input. Hal ini dapat **membatasi penemuan item di luar minat yang sudah ada**, sehingga pengguna mungkin tidak diperkenalkan dengan variasi atau genre baru.
  * **Ketergantungan pada Kualitas Deskripsi Item:** Kualitas rekomendasi sangat bergantung pada kekayaan dan akurasi atribut konten item. Jika deskripsi item kurang detail atau tidak lengkap, rekomendasi bisa menjadi kurang efektif dan **menghasilkan rekomendasi yang kurang relevan**.
  * **"Overspecialization":** Jika pengguna hanya menyukai satu jenis item, sistem mungkin akan terus merekomendasikan item dari jenis yang sama secara berulang-ulang, tanpa memperkenalkan variasi.

-----

## **6. Evaluation**

### **Metrik Evaluasi yang Digunakan**

Untuk sistem rekomendasi berbasis *content-based filtering* seperti ini, di mana tidak ada data interaksi pengguna yang eksplisit (misalnya, riwayat pembelian atau rating pengguna terhadap item yang direkomendasikan) untuk memverifikasi preferensi, evaluasi umumnya lebih bersifat **kualitatif** dan berfokus pada **relevansi konten**.

Metrik evaluasi utama yang digunakan dalam proyek ini adalah:

1.  **Relevansi Konten (Content Relevance):**
      * **Penjelasan:** Metrik ini mengukur sejauh mana buku-buku yang direkomendasikan secara semantik atau tematik mirip dengan buku input. Semakin tinggi relevansinya, semakin baik kinerja sistem dalam menemukan "kembaran" konten.
      * **Cara Metrik Bekerja (Formula & Cara Kerja):**
        Dalam konteks ini, Relevansi Konten dievaluasi secara **manual/subjektif** melalui **inspeksi visual terhadap daftar rekomendasi**. Tidak ada formula matematis langsung yang diterapkan karena tidak ada *ground truth* eksplisit tentang "buku mana yang seharusnya direkomendasikan" dari dataset ini.
          * Kami memilih sebuah buku input (misalnya, 'The Hobbit').
          * Sistem menghasilkan daftar $N$ (dalam kasus ini, $N=10$) buku yang paling mirip berdasarkan *cosine similarity* dari `combined_features`.
          * Kami kemudian secara manual memeriksa judul dan penulis dari $N$ buku yang direkomendasikan.
          * Relevansi dinilai berdasarkan apakah buku-buku yang direkomendasikan memiliki penulis yang sama, genre yang serupa (fantasi, fiksi ilmiah, roman, dll., yang dapat diinferensi dari judul dan penulis), atau tema yang serupa dengan buku input. Jika sebagian besar rekomendasi memenuhi kriteria kesamaan ini, sistem dianggap relevan.
          * **Cosine Similarity (Rumus):** Meskipun relevansi diinterpretasi secara manual, dasarnya adalah *cosine similarity*. Rumus *cosine similarity* antara dua vektor $A$ dan $B$ (dalam kasus ini, vektor TF-IDF dari dua buku) didefinisikan sebagai:
            $$\text{similarity}(A, B) = \frac{A \cdot B}{\|A\| \|B\|} = \frac{\sum_{i=1}^{n} A_i B_i}{\sqrt{\sum_{i=1}^{n} A_i^2} \sqrt{\sum_{i=1}^{n} B_i^2}}$$
            Di mana:
              * $A\_i$ dan $B\_i$ adalah komponen $i$ dari vektor $A$ dan $B$ (yaitu, bobot TF-IDF dari kata tertentu).
              * $A \\cdot B$ adalah produk dot dari vektor $A$ dan $B$.
              * $|A|$ dan $|B|$ adalah norma (panjang) Euclidean dari vektor $A$ dan $B$.
                Nilai *cosine similarity* berkisar antara -1 (berlawanan) hingga 1 (sangat mirip). Dalam konteks TF-IDF, yang bobotnya non-negatif, nilai ini berkisar antara 0 hingga 1. Semakin dekat nilai ke 1, semakin tinggi kemiripan konten antara dua buku.

### **Hasil Proyek Berdasarkan Metrik Evaluasi**

Berdasarkan **inspeksi visual dan analisis relevansi konten** dari contoh penggunaan model ('The Hobbit'):

Output rekomendasi untuk 'The Hobbit' secara konsisten menampilkan buku-buku lain yang ditulis oleh J.R.R. Tolkien, seperti seri 'The Lord of the Rings' dan karya-karya lain yang berkaitan dengan Middle-earth ('The Silmarillion', 'Unfinished Tales', dll.). Ini menunjukkan bahwa model berhasil menangkap kesamaan berdasarkan penulis dan, secara implisit, genre (fantasi epik).

Ini adalah indikator yang kuat bahwa:

  * **Representasi Fitur (TF-IDF):** Proses *TF-IDF Vectorization* berhasil mengubah `combined_features` menjadi representasi numerik yang efektif, menangkap esensi konten buku.
  * **Perhitungan Kesamaan (Cosine Similarity):** Metrik *cosine similarity* berhasil mengidentifikasi buku-buku yang memiliki kesamaan konten tinggi berdasarkan representasi vektor TF-IDF mereka.

**Kesimpulan Evaluasi:**
Model rekomendasi *content-based filtering* ini **berhasil memenuhi tujuannya** untuk menyarankan buku-buku yang memiliki kemiripan konten dengan buku input. Meskipun tidak ada metrik kuantitatif berbasis interaksi pengguna yang digunakan, relevansi yang tinggi dari rekomendasi yang dihasilkan secara manual membuktikan efektivitas pendekatan ini dalam konteks proyek berbasis konten.

-----
