# Laporan Proyek Machine Learning - Salwa Sabira

## **Project Overview**

Perkembangan industri literasi digital dan peningkatan minat baca masyarakat mendorong kebutuhan akan sistem rekomendasi buku yang efektif. Dalam platform seperti Goodreads, ribuan buku tersedia, dan pengguna seringkali mengalami kesulitan memilih bacaan yang sesuai minat mereka. Sistem rekomendasi dapat membantu pengguna menemukan buku yang relevan berdasarkan preferensi atau minat sebelumnya.

Proyek ini bertujuan membangun sistem rekomendasi buku berbasis *content-based filtering* menggunakan data publik dari Goodreads yang tersedia di Kaggle:
[https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks](https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks)

Referensi:

* Jannach, D., & Adomavicius, G. (2016). Recommendation systems: Challenges, insights and research opportunities. *Elsevier, Data & Knowledge Engineering*.
* Ricci, F., Rokach, L., & Shapira, B. (2011). *Introduction to Recommender Systems Handbook*. Springer.

## **Business Understanding**

### **Problem Statements**

1. Bagaimana pengguna dapat menemukan buku yang relevan dengan preferensi mereka dari ribuan pilihan yang tersedia?
2. Bagaimana membangun sistem rekomendasi buku yang tidak hanya mengandalkan rating, tetapi juga konten buku seperti judul, penulis, dan jumlah ulasan?

### **Goals**

1. Menyediakan sistem rekomendasi buku berbasis konten yang mampu merekomendasikan buku mirip berdasarkan fitur-fitur deskriptif.
2. Menyederhanakan proses pencarian buku dengan menyarankan Top-N buku yang relevan.

### **Solution Approach**

* **Pendekatan 1: Content-Based Filtering**
  Menggunakan fitur-fitur buku seperti judul, penulis, jumlah rating, dan review untuk menghitung kemiripan antar buku dengan TF-IDF dan cosine similarity.

* **Pendekatan 2: Collaborative Filtering (Untuk Pengembangan Lanjutan)**
  Mengandalkan data interaksi pengguna (rating atau ulasan) untuk menyarankan buku serupa yang dinikmati oleh pengguna lain.

## **Data Understanding**

Dataset diambil dari platform Kaggle:
[https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks](https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks)

Dataset berisi informasi tentang 11.119 buku dengan 12 kolom, antara lain:

* `bookID`: ID unik buku
* `title`: Judul buku
* `authors`: Penulis
* `average_rating`: Rata-rata rating buku
* `isbn`, `isbn13`: Nomor ISBN
* `language_code`: Bahasa buku
* `num_pages`: Jumlah halaman
* `ratings_count`: Jumlah rating
* `text_reviews_count`: Jumlah ulasan teks
* `publication_date`: Tanggal terbit
* `publisher`: Nama penerbit

### **Pemeriksaan Data**

* **Missing values**: Telah dilakukan pengecekan menggunakan `df.isna().sum()`, dan **tidak ditemukan missing values** di seluruh kolom.
* **Duplikat**: Telah dilakukan pengecekan menggunakan `df.duplicated().sum()`, dan **tidak ditemukan data duplikat**.
* **Distribusi bahasa**: Data didominasi oleh buku berbahasa Inggris (kode `en` dan `eng`), yang difilter untuk fokus model.
* **Distribusi ratings**: Mayoritas buku memiliki rating antara 3.5–4.5.
* **Distribusi jumlah ulasan** dan rating berskala log karena nilai sangat bervariasi antar buku.

---

## **Data Preparation**

Berikut adalah tahapan preprocessing data:

1. **Menghapus kolom tidak relevan**

   * Kolom seperti `bookID`, `isbn`, `isbn13`, `publication_date`, `publisher`, `num_pages` dihapus karena tidak berkontribusi terhadap pemodelan.
  
2. **Filter bahasa**

   * Hanya buku dengan `language_code` = `'en'` atau `'eng'` yang disertakan untuk memastikan konsistensi konten.

3. **Cek Anomali**

   * Cek rating yang tidak normal dan filter buku dengan rating yang valid.

4. **Pembuatan kolom gabungan fitur (`combined_features`)**

   * Digabungkan `title`, `authors`, `language_code`, dan nilai numerik penting menjadi satu kolom teks deskriptif.

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

---

## **Modeling**

### **Pendekatan: Content-Based Filtering**

Model yang digunakan:

* **TF-IDF Vectorizer**: Mengubah teks pada kolom `combined_features` menjadi representasi numerik berbobot.
* **Cosine Similarity**: Mengukur kemiripan antar buku berdasarkan vektor TF-IDF.

#### **Cara Kerja Inti:**

1. Buku direpresentasikan sebagai vektor berdasarkan kata-kata unik yang ada di `combined_features`.
2. Kemiripan antar buku dihitung menggunakan **cosine similarity** — semakin dekat sudut antar vektor, semakin mirip buku tersebut.
3. Untuk setiap buku input, sistem akan mengembalikan Top-N buku yang paling mirip berdasarkan nilai cosine similarity tertinggi.

#### **Output**

Fungsi `recommend_books(title)` akan mengembalikan 10 buku paling relevan.

Contoh output untuk input *The Hobbit*:

```
1. The Fellowship of the Ring - J.R.R. Tolkien  
2. The Two Towers - J.R.R. Tolkien  
3. The Return of the King - J.R.R. Tolkien  
... dan seterusnya
```

### **Kelebihan dan Kekurangan**

| Pendekatan              | Kelebihan                                         | Kekurangan                                                        |
| ----------------------- | ------------------------------------------------- | ----------------------------------------------------------------- |
| Content-Based Filtering | Tidak butuh data pengguna, cocok untuk cold-start | Rekomendasi cenderung sempit pada buku serupa, kurang eksploratif |
| Collaborative Filtering | Lebih personal, berdasarkan rating pengguna nyata | Membutuhkan data interaksi pengguna yang banyak dan berkualitas   |

## **Evaluation**

Karena pendekatan *content-based filtering* tidak memiliki label ground truth eksplisit, evaluasi dilakukan dengan cara:

* **Manual inspection** atas hasil rekomendasi.
* **Top-N recommendation** diverifikasi secara kualitatif: apakah hasil yang diberikan benar-benar mirip dan relevan?

Untuk proyek lanjutan, metrik seperti *precision\@k*, *recall\@k*, dan *Mean Average Precision* dapat digunakan jika data interaksi pengguna tersedia.
