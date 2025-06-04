# Laporan Proyek Machine Learning - Salwa Sabira

## **Project Overview**

Proyek ini bertujuan untuk mengembangkan sistem rekomendasi buku dengan pendekatan content-based filtering. Di era digital saat ini, jumlah buku yang tersedia—baik dalam bentuk fisik maupun digital—sangatlah banyak. Hal ini membuat pembaca kesulitan menemukan buku yang sesuai dengan minat mereka. Platform seperti Goodreads menyediakan jutaan judul buku, lengkap dengan rating dan ulasan pengguna. Tanpa sistem rekomendasi yang baik, pengguna bisa melewatkan buku yang sebenarnya cocok untuk mereka, atau malah mendapatkan saran yang kurang relevan.

Sistem rekomendasi hadir sebagai solusi untuk mengatasi masalah tersebut. Dengan menganalisis karakteristik buku-buku yang pernah disukai pengguna, sistem dapat memberikan saran buku lain yang serupa. Dalam proyek ini, pendekatan content-based filtering dipilih karena metode ini memungkinkan personalisasi yang tinggi tanpa memerlukan data historis interaksi pengguna secara besar-besaran. Cukup dengan informasi mendetail dari setiap buku, sistem ini bisa memberikan rekomendasi yang relevan.

Referensi:

* Jannach, D., & Adomavicius, G. (2016). Recommendation systems: Challenges, insights and research opportunities. *Elsevier, Data & Knowledge Engineering*.
* Ricci, F., Rokach, L., & Shapira, B. (2011). *Introduction to Recommender Systems Handbook*. Springer.

-----

## **Business Understanding**

### **Problem Statements**

1. Bagaimana cara menyarankan buku kepada pengguna berdasarkan kesamaan konten buku?
2. Bagaimana memastikan buku-buku yang direkomendasikan relevan dengan preferensi pengguna?
3. Bagaimana mengevaluasi kualitas sistem rekomendasi yang dihasilkan?

### **Goals**

1. Membangun sistem rekomendasi buku menggunakan pendekatan content-based filtering berdasarkan atribut konten seperti judul, penulis, dan bahasa.
2. Menghasilkan rekomendasi buku yang relevan berdasarkan buku yang dipilih pengguna.
3. Mengukur kinerja sistem rekomendasi dengan metrik evaluasi seperti Precision\@K, Recall\@K, F1\@K, dan MAP.

### **Solution Approach**
Untuk mencapai tujuan di atas, dua pendekatan solusi berbasis content-based filtering akan diimplementasikan dan dievaluasi:
* **Pendekatan 1:** TF-IDF Vectorization + Cosine Similarity
Menggunakan Term Frequency-Inverse Document Frequency (TF-IDF) untuk mengubah fitur konten tekstual buku (judul, penulis, bahasa) menjadi representasi vektor numerik.
Menggunakan metrik Cosine Similarity untuk menghitung kemiripan antara vektor-vektor buku dan menghasilkan rekomendasi.
* **Pendekatan 2:** Count Vectorization + Cosine Similarity
Menggunakan Count Vectorization (termasuk N-grams) untuk mengubah fitur konten tekstual buku menjadi representasi vektor numerik berdasarkan frekuensi kata/frasa.
Menggunakan metrik Cosine Similarity untuk menghitung kemiripan antara vektor-vektor buku dan menghasilkan rekomendasi.

Kedua pendekatan ini akan dievaluasi menggunakan metrik yang sama untuk perbandingan kinerja.

-----

## **Data Understanding**

Dataset yang digunakan dalam proyek ini adalah **Goodreads Books** yang diperoleh dari Kaggle, dapat diakses melalui tautan berikut: [https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks](https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks).

Dataset ini mengandung informasi lengkap mengenai **11.119 buku** dan memiliki 23 kolom. Setelah memuat dataset ke dalam *dataframe* dan membersihkan nama kolom dari spasi, berikut adalah informasi mengenai jumlah data dan variabel yang tersedia:


**Kondisi Data Awal:**

  * **Jumlah Data:** 11.119 baris dan 12 kolom.
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

EDA dilakukan untuk memahami distribusi dan karakteristik dari setiap variabel penting dalam dataset, serta untuk mendapatkan insight mendalam yang akan memandu proses pra-pemrosesan dan pengembangan model.

#### **Eksplorasi Variabel `title` dan `authors`**

Dataset ini sangat kaya dengan **10.344 judul buku unik** dan **6.635 penulis yang berbeda**, menunjukkan keragaman konten yang tinggi. Penulis seperti **Stephen King, P.G. Wodehouse, Rumiko Takahashi, dan Orson Scott Card** memiliki kontribusi buku terbanyak, mengindikasikan bahwa karya-karya mereka mungkin memiliki pengaruh signifikan dalam basis data buku ini dan bisa menjadi titik fokus rekomendasi jika pengguna menyukai gaya penulis tertentu.

#### **Eksplorasi Variabel `average_rating`**

Distribusi *average\_rating* menunjukkan bahwa mayoritas buku dalam dataset memiliki rata-rata rating 3.9, dengan puncak yang signifikan di sekitar 5.0. Hal ini menandakan dataset cenderung berisi buku-buku yang umumnya disukai oleh pembaca, yang bisa menjadi keuntungan dalam merekomendasikan buku-buku berkualitas tinggi. Namun, perlu diperhatikan bahwa ada juga buku dengan rating sangat rendah (mendekati 0).

#### **Eksplorasi Variabel `language_code`**

Mayoritas buku ditulis dalam bahasa Inggris (kode 'en' atau 'eng'), sehingga proyek ini hanya akan memfokuskan pada buku-buku dalam bahasa tersebut untuk memastikan hasil yang relevan.

#### **Eksplorasi Variabel `text_reviews_count` dan `ratings_count`**

Kedua variabel ini menunjukkan **distribusi yang sangat condong ke kanan (right-skewed)**, yang berarti sebagian besar buku memiliki jumlah ulasan dan rating yang relatif sedikit, sementara hanya segelintir buku yang sangat populer memiliki jumlah ulasan/rating yang sangat tinggi. Pola ini umum dalam dataset populer, menunjukkan adanya **outlier** pada buku-buku yang sangat populer. Transformasi logaritmik (`np.log1p`) sangat membantu dalam memvisualisasikan distribusi ini dengan lebih baik, namun terlalu ekstrem pada ujung kanan (outlier) menunjukkan potensi bias jika data ini digunakan tanpa penyesuaian, karena buku-buku yang sangat populer mungkin akan lebih sering muncul dalam rekomendasi meskipun kontennya tidak sepenuhnya relevan.

#### **Eksplorasi Variabel `num_pages`**

Mayoritas buku memiliki jumlah halaman antara 200 hingga 400. Meskipun tidak langsung digunakan sebagai fitur konten, informasi ini dapat berguna untuk memfilter buku dengan jumlah halaman yang tidak realistis atau untuk mengidentifikasi buku-buku yang sangat tipis/tebal jika diperlukan dalam kasus penggunaan tertentu.

#### **Eksplorasi Variabel `publisher`**

Publishers seperti **Vintage, Penguin Books, dan Penguin Classic** menerbitkan jumlah buku terbanyak dalam dataset ini. Informasi ini tidak secara langsung menjadi fitur dalam model *content-based filtering* ini, namun dapat memberikan gambaran tentang dominasi penerbit tertentu dan potensi bias koleksi buku dalam dataset.

-----

## **Data Preparation**

### **Teknik Data Preparation yang Dilakukan:**

1.  **Penghapusan Kolom Tidak Relevan:**

      * **Teknik:** Menghapus kolom `bookID`, `isbn`, `isbn13`, `num_pages`, `publication_date`, dan `publisher`.
      * **Tujuan:** Kolom-kolom ini dihapus karena **tidak mengandung informasi relevan** yang secara langsung berkontribusi pada kesamaan konten dalam sistem rekomendasi *content-based*. Misalnya, `bookID` atau `isbn` adalah identifikasi unik buku, bukan deskripsi konten. `num_pages`, `publication_date`, dan `publisher` meskipun merupakan atribut buku, tidak digunakan dalam definisi "konten" untuk sistem rekomendasi ini dan hanya akan membebani model dengan variabel yang tidak berguna atau mengurangi fokus pada karakteristik utama seperti judul, penulis, rating, dan ulasan teks.
      * **Implementasi:**
        ```python
        df.drop(columns=['bookID', 'isbn', 'isbn13','num_pages', 'publication_date','publisher'], inplace=True)
        ```
     * **Alasan:** Kolom-kolom ini dianggap tidak secara langsung berkontribusi pada fitur konten inti (judul, penulis, bahasa) yang akan digunakan untuk menghitung kemiripan dalam model content-based filtering ini. Penghapusan ini juga menyederhanakan dataset.
2.  **Pemfilteran Buku Berdasarkan Bahasa:**

      * **Teknik:** Mempertahankan hanya buku dengan `language_code` 'en' atau 'eng'.
      * **Tujuan:** Seperti yang terlihat pada EDA, bahasa Inggris sangat mendominasi dataset. Pemfilteran ini dilakukan untuk **memastikan relevansi rekomendasi bagi pengguna berbahasa Inggris**, yang merupakan target utama dari sistem ini. Dengan fokus pada satu bahasa, kita **menghindari kompleksitas dan potensi *noise*** dari buku-buku dengan bahasa yang berbeda, yang mungkin tidak dipahami oleh mayoritas pengguna, sehingga **meningkatkan kualitas dan koherensi rekomendasi**.
      * **Implementasi:**
        ```python
        df = df[df['language_code'].isin(['en', 'eng'])]
        ```
      * **Alasan:** EDA menunjukkan mayoritas buku berbahasa Inggris. Pemfokusan pada satu bahasa ini bertujuan untuk meningkatkan relevansi rekomendasi bagi target pengguna utama dan mengurangi kompleksitas pemrosesan bahasa yang beragam.

3.  **Penanganan Anomali `average_rating`:**

      * **Teknik:** Memfilter buku dengan `average_rating` antara nilai valid (\>0 dan \<=5).
      * **Tujuan:** Meskipun EDA menunjukkan distribusi rating yang baik, beberapa entri mungkin memiliki nilai `average_rating` yang tidak logis (misalnya 0, atau nilai di luar rentang standar 1-5 yang umum digunakan pada platform rating). Pemfilteran ini dilakukan untuk **memastikan bahwa data rating yang digunakan valid dan konsisten**, sehingga tidak menimbulkan bias atau kesalahan dalam perhitungan kesamaan konten yang menggunakan rating sebagai salah satu fitur.
      * **Implementasi:**
        ```python
        df = df[(df['average_rating'] > 0) & (df['average_rating'] <= 5)]
      * **Alasan:** Untuk memastikan bahwa data rating yang digunakan konsisten dan logis, sehingga tidak menimbulkan bias atau error dalam analisis atau jika rating akan digunakan sebagai fitur (meskipun dalam implementasi akhir combined_features tidak menyertakannya secara eksplisit).        ```

4.  **Pembentukan `combined_features`:**
      * **Teknik:** Menggabungkan kolom `title`, `authors`, `language_code`, `average_rating`, `ratings_count`, dan `text_reviews_count` menjadi satu kolom teks baru bernama `combined_features`. Kolom numerik dikonversi ke string sebelum digabungkan.
      * **Tujuan:** Langkah ini sangat krusial karena `TF-IDF Vectorizer` hanya dapat memproses data dalam format teks. Dengan menggabungkan beberapa fitur yang relevan menjadi satu string, kita membuat representasi komprehensif dari setiap buku yang akan menjadi dasar bagi *TF-IDF* untuk mengekstrak kata kunci dan konsep. Ini memungkinkan *TF-IDF* untuk menangkap kesamaan tidak hanya dari judul dan penulis tapi juga bahasa, yang semuanya berkontribusi pada definisi "konten" dalam konteks ini.
      * **Implementasi:**
        ```python
        df['combined_features'] = (
            df['title'] + ' ' +
            df['authors'] + ' ' +
            df['language_code']
        )
        ```
     * **Alasan:** Untuk membuat representasi tekstual yang komprehensif dari setiap buku. Fitur gabungan inilah yang akan diproses oleh vectorizer untuk mengekstrak fitur numerik yang dapat dibandingkan.
5. **Vectorization Fitur Teks** 
Dua teknik vectorization diterapkan pada combined_features untuk mengubah data teks menjadi format numerik yang dapat diproses oleh algoritma machine learning:

1). **TF-IDF Vectorization**

*TF-IDF (Term Frequency-Inverse Document Frequency)* adalah teknik untuk mengubah koleksi dokumen teks (dalam kasus ini, `combined_features` dari setiap buku) menjadi representasi numerik (vektor fitur). Bobot TF-IDF mencerminkan seberapa penting sebuah kata dalam sebuah dokumen relatif terhadap koleksi dokumen keseluruhan. Kata-kata umum (seperti "the", "a", "is") akan memiliki bobot rendah, sementara kata-kata unik dan deskriptif akan memiliki bobot tinggi. `stop_words='english'` digunakan untuk menghapus kata-kata umum yang tidak memberikan informasi berarti

```python
vectorizer = TfidfVectorizer(stop_words='english')
tfidf_matrix = vectorizer.fit_transform(df['combined_features'])
 ```
`tfidf_matrix` sekarang berisi representasi numerik dari setiap buku, di mana setiap baris merepresentasikan sebuah buku dan setiap kolom merepresentasikan sebuah kata dengan bobot TF-IDF-nya.

  * **Alasan:** TF-IDF membobot kata berdasarkan frekuensinya dalam satu dokumen (buku) dan frekuensi inversnya di seluruh korpus (kumpulan buku). Ini membantu menyoroti kata-kata yang penting dan khas untuk setiap buku.

2). **Count Vectorization**

`CountVectorizer` dari scikit-learn mengubah fitur teks gabungan (`combined_features`) menjadi `count_matrix` numerik. Proses ini melibatkan tokenisasi teks menjadi kata tunggal (unigram) dan pasangan kata (bigram) sesuai `ngram_range=(1, 2)`, penghapusan kata umum bahasa Inggris (`stop_words='english'`), dan pengabaian token yang muncul di kurang dari dua dokumen (`min_df=2`). Hasilnya, setiap buku direpresentasikan sebagai vektor frekuensi dari token-token yang telah disaring ini, yang kemudian digunakan untuk menghitung kemiripan.
     
   * **Alasan:** Count Vectorization memberikan representasi berdasarkan frekuensi kemunculan kata/frasa. Penggunaan bigram dapat membantu menangkap konteks yang lebih baik daripada kata tunggal saja.
-----

## **Modeling and Results**

### **Membangun Sistem Rekomendasi Content-Based Filtering**
Pada tahap ini, model sistem rekomendasi content-based filtering dibangun menggunakan fitur-fitur yang telah dipersiapkan. Dua pendekatan solusi diimplementasikan:

### **Pendekatan 1: TF-IDF Vectorization + Cosine Similarity**

Setelah data diproses dengan TF-IDF, langkah selanjutnya adalah membangun model content-based filtering menggunakan Cosine Similarity:

**Menghitung Cosine Similarity:**

*Cosine Similarity* adalah metrik yang mengukur kemiripan antara dua vektor non-nol dalam ruang vektor. Semakin dekat nilai *cosine similarity* ke 1, semakin mirip kedua item. Dalam konteks ini, kita menghitung *cosine similarity* antara setiap pasang buku berdasarkan vektor TF-IDF mereka. `linear_kernel` digunakan karena lebih cepat untuk menghitung *cosine similarity* ketika vektor sudah dinormalisasi (seperti hasil dari TF-IDF), **bertujuan untuk efisiensi komputasi** pada matriks yang besar.
**Implementasi:**
        ```python
        from sklearn.metrics.pairwise import linear_kernel
        cosine_sim = linear_kernel(tfidf_matrix, tfidf_matrix)
        print("Shape Cosine Similarity matrix:", cosine_sim.shape)
        ```
        `cosine_sim` adalah matriks simetri yang menunjukkan tingkat kemiripan antara setiap buku dengan buku lainnya, menjadi inti dari mekanisme rekomendasi kita.

**Fungsi Rekomendasi**

Fungsi `recommend_books` dibuat untuk mengambil judul buku sebagai input dan mengembalikan 10 buku teratas yang paling mirip berdasarkan skor *cosine similarity*.

```python
def recommend_books_tfidf(title, cosine_sim=cosine_sim, df=df):
    book_row = df[df['title'].str.lower() == title.lower()]

    if book_row.empty:
        return "Book not found."
    idx = book_row.index[0]
    try:
        positional_idx = df.index.get_loc(idx)
    except KeyError:
        titles_list = df['title'].str.lower().tolist()
        try:
             positional_idx = titles_list.index(title.lower())
        except ValueError:
             return "Book not found (positional index error)."

    sim_scores = list(enumerate(cosine_sim[positional_idx]))
    sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)
    sim_scores = sim_scores[1:11]
    recommended_book_original_indices = [df.index[i[0]] for i in sim_scores]
    recommended_book_indices_in_filtered_df = [i[0] for i in sim_scores]
    return df[['title', 'authors']].iloc[recommended_book_indices_in_filtered_df]
```

**Contoh Penggunaan Model (Top-N Recommendation)**

Berikut adalah contoh rekomendasi untuk buku 'The Hobbit':

```python
print("\ntf-idf Recommendations untuk 'The Hobbit':")
print(recommend_books_tfidf('The Hobbit'))
```

**Output:**

```
tf-idf Recommendations untuk 'The Hobbit':

                                                  title  \
1699                The Hobbit: Or There and Back Again   
1700                                         The Hobbit   
1698                              Poems From The Hobbit   
6269               The Hobbit  or  There and Back Again   
4271                 The Lord of the Rings / The Hobbit   
1697                               The Annotated Hobbit   
21    J.R.R. Tolkien 4-Book Boxed Set: The Hobbit an...   
4595                         Pictures by J.R.R. Tolkien   
721                       The Letters of J.R.R. Tolkien   
5254                                   The Silmarillion   

                                                authors  
1699                                     J.R.R. Tolkien  
1700                                     J.R.R. Tolkien  
1698                                     J.R.R. Tolkien  
6269                           J.R.R. Tolkien/Alan  Lee  
4271                                     J.R.R. Tolkien  
1697                 J.R.R. Tolkien/Douglas A. Anderson  
21                                       J.R.R. Tolkien  
4595                 J.R.R. Tolkien/Christopher Tolkien  
721   J.R.R. Tolkien/Humphrey Carpenter/Christopher ...  
5254                 J.R.R. Tolkien/Christopher Tolkien
```

**Kelebihan:**

* **Pembobotan Signifikansi Kata**: TF-IDF memberikan bobot lebih tinggi pada kata-kata yang sering muncul dalam satu dokumen (**TF - Term Frequency**) tetapi jarang muncul di dokumen-dokumen lain dalam keseluruhan koleksi (**IDF - Inverse Document Frequency**). Ini berarti kata-kata yang lebih unik dan deskriptif untuk suatu dokumen akan memiliki pengaruh lebih besar dalam perhitungan kemiripan, sementara kata-kata umum (seperti "yang", "dan", "di") akan mendapatkan bobot rendah (atau dihilangkan jika menggunakan *stop words*).
* **Mengurangi Noise dari Kata Umum**: Dengan adanya komponen IDF, kata-kata yang umum di banyak dokumen secara otomatis mendapat bobot lebih rendah, sehingga mengurangi "kebisingan" dan membantu fokus pada kata-kata yang lebih membedakan.
* **Performa Baik pada Umumnya**: Seringkali memberikan hasil yang baik dan relevan untuk berbagai tugas pencarian informasi dan sistem rekomendasi berbasis konten karena kemampuannya menangkap pentingnya kata secara kontekstual dalam korpus.

**Kekurangan:**

* **Tidak Memperhatikan Urutan Kata**: Sama seperti Count Vectorizer, TF-IDF dasar tidak memperhitungkan urutan kata atau struktur semantik kalimat. Dua kalimat dengan kata yang sama tetapi urutan berbeda (dan makna berbeda) bisa dianggap mirip.
* **Vocabulary Besar**: Jika tidak ada penyaringan (seperti `min_df`), vocabulary bisa menjadi sangat besar, terutama dengan korpus yang beragam, yang dapat mempengaruhi kebutuhan memori dan komputasi.
* **Kurang Efektif untuk Teks Pendek**: IDF bisa menjadi kurang stabil atau kurang bermakna jika dokumen (teks) sangat pendek karena frekuensi kata menjadi kurang representatif.
* **Tidak Menangkap Sinonim atau Makna Terkait**: TF-IDF bekerja pada level kata. Kata "mobil" dan "kendaraan roda empat" akan dianggap berbeda total meskipun maknanya mirip, kecuali ada teknik tambahan seperti penggunaan Word Embeddings.

---

### **Pendekatan 2: Count Vectorization + Cosine Similarity**

Setelah data diproses dengan Count Vectorization, langkah selanjutnya adalah membangun model content-based filtering menggunakan Cosine Similarity:

**Menghitung Cosine Similarity:**

*Cosine Similarity* adalah metrik yang mengukur kemiripan antara dua vektor non-nol dalam ruang vektor. Semakin dekat nilai *cosine similarity* ke 1, semakin mirip kedua item. Dalam konteks ini, kita menghitung *cosine similarity* antara setiap pasang buku berdasarkan vektor TF-IDF mereka. `linear_kernel` digunakan karena lebih cepat untuk menghitung *cosine similarity* ketika vektor sudah dinormalisasi (seperti hasil dari TF-IDF), **bertujuan untuk efisiensi komputasi** pada matriks yang besar.
**Implementasi:**
        ```python
        from sklearn.metrics.pairwise import linear_kernel
        cosine_sim = linear_kernel(tfidf_matrix, tfidf_matrix)
        print("Shape Cosine Similarity matrix:", cosine_sim.shape)
        ```
        `cosine_sim` adalah matriks simetri yang menunjukkan tingkat kemiripan antara setiap buku dengan buku lainnya, menjadi inti dari mekanisme rekomendasi kita.

**Fungsi Rekomendasi**

Fungsi `recommend_books` dibuat untuk mengambil judul buku sebagai input dan mengembalikan 10 buku teratas yang paling mirip berdasarkan skor *cosine similarity*.

```python
def recommend_books_count(title, cosine_sim=cosine_sim_count, df=df, k=10):
    book_row = df[df['title'].str.lower() == title.lower()]
    if book_row.empty:         # judul tidak ditemukan
        return "Book not found."

    idx = book_row.index[0]                 # index asli
    positional_idx = df.index.get_loc(idx)  # posisi baris pada df ter-fi lter

    sim_scores = list(enumerate(cosine_sim[positional_idx]))
    sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)[1:k+1]  # skip diri sendiri

    rec_indices = [i[0] for i in sim_scores]
    return df[['title', 'authors']].iloc[rec_indices]
```

**Contoh Penggunaan Model (Top-N Recommendation)**

Berikut adalah contoh rekomendasi untuk buku 'The Hobbit':

```python
print("\nCount-Based Recommendations untuk 'The Hobbit':")
print(recommend_books('The Hobbit'))
```

**Output:**

```

Count-Based Recommendations untuk 'The Hobbit':
                                                  title  \
1699                The Hobbit: Or There and Back Again   
1700                                         The Hobbit   
1698                              Poems From The Hobbit   
4271                 The Lord of the Rings / The Hobbit   
6261                      Tales from the Perilous Realm   
1697                               The Annotated Hobbit   
2026                      Letters from Father Christmas   
6269               The Hobbit  or  There and Back Again   
21    J.R.R. Tolkien 4-Book Boxed Set: The Hobbit an...   
4595                         Pictures by J.R.R. Tolkien   

                                 authors  
1699                      J.R.R. Tolkien  
1700                      J.R.R. Tolkien  
1698                      J.R.R. Tolkien  
4271                      J.R.R. Tolkien  
6261                      J.R.R. Tolkien  
1697  J.R.R. Tolkien/Douglas A. Anderson  
2026      J.R.R. Tolkien/Baillie Tolkien  
6269            J.R.R. Tolkien/Alan  Lee  
21                        J.R.R. Tolkien  
4595  J.R.R. Tolkien/Christopher Tolkien   
```

**Kelebihan:**

* **Sederhana dan Cepat**: Konsepnya sangat lugas, yaitu hanya menghitung frekuensi kemunculan kata. Implementasinya juga cenderung lebih cepat dibandingkan metode yang lebih kompleks.
* **Dasar yang Baik**: Merupakan representasi dasar yang baik untuk teks dan seringkali menjadi *baseline* yang bagus untuk dibandingkan dengan teknik lain.
* **Bisa Menangkap Konteks Lokal dengan N-grams**: Jika dikombinasikan dengan N-grams (seperti bigram atau trigram, contoh `ngram_range=(1,2)`), Count Vectorizer dapat menangkap beberapa konteks lokal dan frasa pendek. Misalnya, "machine learning" sebagai bigram akan dianggap berbeda dari kata "machine" dan "learning" secara terpisah.

**Kekurangan:**

* **Memberi Bobot Sama pada Semua Kata (Relatif)**: Kata-kata yang sangat umum (seperti "the", "is", "a" jika tidak dihilangkan dengan *stop words*) akan memiliki frekuensi tinggi dan bisa mendominasi perhitungan kemiripan, meskipun kata tersebut tidak terlalu informatif untuk membedakan konten.
* **Tidak Membedakan Pentingnya Kata**: Tidak ada mekanisme bawaan (seperti IDF pada TF-IDF) untuk menilai seberapa penting atau unik sebuah kata dalam keseluruhan koleksi dokumen. Kata yang muncul di semua dokumen akan tetap dihitung frekuensinya di masing-masing dokumen.
* **Rentan terhadap Panjang Dokumen**: Dokumen yang lebih panjang secara alami akan memiliki jumlah kata yang lebih tinggi, yang dapat mempengaruhi perhitungan kemiripan jika tidak dinormalisasi dengan baik (meskipun Cosine Similarity sendiri membantu dalam normalisasi ini).
* **Tidak Memperhatikan Urutan Kata (untuk Unigram)**: Jika hanya menggunakan unigram (kata tunggal), urutan kata dan makna kalimat akan hilang.
* **Tidak Menangkap Sinonim**: Sama seperti TF-IDF, Count Vectorizer tidak dapat mengidentifikasi sinonim atau kata-kata dengan makna serupa.

----

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

## **Evaluation**


### **1. Pembuatan Ground Truth**
Karena ini adalah sistem berbasis konten dan tidak ada data interaksi eksplisit pengguna (seperti "buku yang benar-benar dibaca dan disukai setelah melihat rekomendasi A"), ground truth disimulasikan untuk tujuan evaluasi. Caranya:

1. Dipilih 100 buku terpopuler dari dataset (berdasarkan 'ratings_count').
2. Untuk setiap buku populer ini, 10 buku teratas yang direkomendasikan oleh model TF-IDF + Cosine Similarity (menggunakan fungsi recommend_books) dianggap sebagai "item yang relevan" atau ground truth. Pendekatan ini memungkinkan perbandingan kinerja antara model TF-IDF (yang digunakan untuk membuat ground truth) dan model Count Vectorizer terhadap basis relevansi yang sama.

### **2. Metrik Evaluasi yang Digunakan**

Sistem rekomendasi ini dievaluasi menggunakan metrik-metrik yang umum digunakan dalam sistem rekomendasi:

1. Precision@K (P@K) \
Mengukur seberapa banyak dari K item yang direkomendasikan adalah item yang benar-benar relevan. Metrik ini menilai ketepatan dari rekomendasi yang diberikan pada K posisi teratas.

**Formula:**
$$\text{Precision@K} = \frac{\text{Jumlah item relevan dalam K rekomendasi teratas}}{K}$$

2. Recall@K (R@K) \
Mengukur proporsi item relevan yang berhasil ditemukan dan direkomendasikan dalam K item teratas, dibandingkan dengan total item relevan yang ada. Metrik ini menilai kelengkapan atau cakupan rekomendasi.

**Formula**:
$$\text{Recall@K} = \frac{\text{Jumlah item relevan dalam K rekomendasi teratas}}{\text{Total jumlah item relevan dalam ground truth}}$$


3. F1@K \
Merupakan rata-rata harmonik dari Precision@K dan Recall@K. Rata-rata harmonik digunakan karena memberikan bobot yang seimbang pada kedua metrik dan cenderung lebih rendah jika salah satu metrik memiliki nilai yang sangat rendah, sehingga cocok untuk kasus di mana keseimbangan antara presisi dan recall diinginkan.

**Formula**:
$$\text{F1@K} = 2 \times \frac{\text{Precision@K} \times \text{Recall@K}}{\text{Precision@K} + \text{Recall@K}}$$

4. MAP@K (Mean Average Precision at K) \
MAP@K memberikan skor rata-rata dari Average Precision untuk semua query. AP@K sendiri mempertimbangkan urutan item relevan dalam daftar rekomendasi; item relevan yang muncul di posisi lebih atas mendapat bobot lebih tinggi. Ini metrik yang baik untuk menilai performa ranking.

**Formula**:
$$\text{AP@K} = \frac{\sum_{i=1}^{K} (\text{P@}i \times \text{rel}(i))}{\text{Jumlah item relevan dalam ground truth}}$$

di mana :
- $P@i$ adalah presisi pada posisi ke-i dalam daftar rekomendasi. 
- $\text{rel}(i)$ bernilai 1 jika item pada posisi ke-i adalah relevan, dan 0 jika tidak.
  
$$\text{MAP@K} = \frac{\sum_{u=1}^{|U|} \text{AP@K}_u}{|U|}$$

di mana:
 - $|U|$ adalah jumlah total query atau pengguna yang dievaluasi.
 - $\text{AP@K}_u$ adalah Average Precision@K untuk query atau pengguna ke-u

---

### **2. Hasil Evaluasi dan Interpretasi**

Berikut adalah hasil evaluasi untuk kedua model dengan K=5 untuk Precision, Recall, F1, dan K=10 untuk MAP.

### Model 1: TF-IDF + Cosine Similarity

**Hasil:**

| Metrik       | Nilai      |
| ------------ | ---------- |
| Precision\@5 | **0.888**  |
| Recall\@5    | **0.4949** |
| F1\@5        | **0.6323** |
| MAP\@10      | **0.8374** |

**Interpretasi:**

* **Precision\@5 sebesar 88.8%** menunjukkan bahwa hampir 9 dari 10 rekomendasi yang diberikan sistem benar-benar relevan.
* **Recall\@5 sebesar 49.5%** menunjukkan bahwa hampir separuh dari seluruh item relevan berhasil ditemukan oleh sistem di antara 5 rekomendasi teratas.
* **F1\@5 sebesar 63.2%** mencerminkan keseimbangan cukup baik antara presisi tinggi dan recall yang moderat.
* **MAP\@10 sebesar 83.7%** menandakan bahwa sistem tidak hanya memberikan item yang relevan, tetapi juga mampu menempatkannya di posisi awal dalam daftar rekomendasi, yang penting untuk pengalaman pengguna.

### Model 2: Count Vectorization + Cosine Similarity

Evaluasi dilakukan terhadap ground truth yang sama, yang dibuat menggunakan TF-IDF

**Hasil:**

| Metrik       | Nilai      |
| ------------ | ---------- |
| Precision\@5 | **0.7599**  |
| Recall\@5    | **0.4311** |
| F1\@5        | **0.5468** |
| MAP\@10      | **0.5482** |

**Interpretasi:**

* **Precision@5 (76.0%):** Model Count Vectorizer menunjukkan bahwa rata-rata 76% dari 5 buku yang direkomendasikannya cocok dengan ground truth (yang dibuat oleh TF-IDF). Angka ini cukup baik, meskipun sedikit di bawah model TF-IDF.
* **Recall@5 (43.1%):** Sistem ini berhasil menemukan sekitar 43.1% dari item yang dianggap relevan dalam 5 rekomendasi teratas. Ini juga sedikit lebih rendah dibandingkan TF-IDF.
* **F1@5 (54.7%):** Skor F1 menunjukkan keseimbangan antara presisi dan recall untuk model Count Vectorizer.
* **MAP@10 (54.8%):** Kualitas peringkat rekomendasi dari model Count Vectorizer, sebagaimana diukur oleh MAP@10, juga lebih rendah dibandingkan TF-IDF. Ini mengindikasikan bahwa TF-IDF lebih baik dalam menempatkan item yang sangat relevan di posisi yang lebih tinggi dalam daftar rekomendasi.

### **3. Kesesuaian Metrik dengan Konteks**

Metrik-metrik yang digunakan sangat tepat untuk sistem rekomendasi berbasis konten karena:

* **Content-based filtering** tidak bergantung pada interaksi eksplisit pengguna, sehingga pengukuran akurasi rekomendasi dilakukan terhadap kemiripan konten.
* **Precision dan Recall** menilai seberapa relevan dan lengkap hasil rekomendasi.
* **MAP** mempertimbangkan urutan rekomendasi, yang penting karena pengguna cenderung hanya melihat item teratas.

Metrik ini mendukung **tujuan solusi** yaitu menyarankan buku-buku yang relevan dan berguna kepada pengguna berdasarkan kemiripan konten deskripsi.

---

### **4. Kesimpulan Evaluasi**

Berdasarkan hasil evaluasi:

* **Model TF-IDF + Cosine Similarity** menunjukkan kinerja yang sangat baik terhadap ground truth yang dibuat berdasarkan dirinya sendiri, yang mengindikasikan konsistensi internal yang tinggi dan berfungsi sebagai baseline yang kuat.
* **Model Count Vectorization + Cosine Similarity**, meskipun menunjukkan performa yang layak (misalnya, Precision@5 sebesar 76.0% dan MAP@10 sebesar 54.8%), secara konsisten memiliki skor yang lebih rendah di semua metrik evaluasi dibandingkan dengan model TF-IDF ketika diukur terhadap ground truth yang dihasilkan oleh TF-IDF.

--- 

### Kesimpulan 
Kedua pendekatan content-based filtering yang diimplementasikan mampu menghasilkan rekomendasi buku. Namun, dalam konteks evaluasi ini (di mana ground truth dibuat menggunakan TF-IDF), model TF-IDF + Cosine Similarity menunjukkan keunggulan yang jelas dibandingkan model Count Vectorization + Cosine Similarity. Hal ini mungkin disebabkan oleh kemampuan TF-IDF untuk memberikan bobot yang lebih bernuansa pada kata-kata (mempertimbangkan seberapa unik sebuah kata di seluruh koleksi buku), yang mungkin menghasilkan pemahaman "kemiripan" yang lebih selaras dengan ground truth yang dihasilkannya. Sementara itu, Count Vectorization, meskipun menggunakan n-gram (bigram), mungkin tidak menangkap signifikansi kata seefektif TF-IDF dalam kasus ini.

Secara keseluruhan, proyek ini berhasil membangun dan mengevaluasi dua varian sistem rekomendasi buku berbasis konten. Model berbasis TF-IDF terbukti lebih efektif berdasarkan metrik yang digunakan dan metodologi ground truth yang diterapkan.
