# Laporan Proyek Machine Learning - Salwa Sabira

## **1. Project Overview**

Perkembangan industri literasi digital dan peningkatan minat baca masyarakat mendorong kebutuhan akan sistem rekomendasi buku yang efektif. Dalam platform seperti Goodreads, ribuan buku tersedia, dan pengguna seringkali mengalami kesulitan memilih bacaan yang sesuai minat mereka. Sistem rekomendasi dapat membantu pengguna menemukan buku yang relevan berdasarkan preferensi atau minat sebelumnya.

Proyek ini bertujuan membangun sistem rekomendasi buku berbasis *content-based filtering* menggunakan data publik dari Goodreads yang tersedia di Kaggle:
[https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks](https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks)

Referensi:

* Jannach, D., & Adomavicius, G. (2016). Recommendation systems: Challenges, insights and research opportunities. *Elsevier, Data & Knowledge Engineering*.
* Ricci, F., Rokach, L., & Shapira, B. (2011). *Introduction to Recommender Systems Handbook*. Springer.

## **2. Business Understanding**

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

## **3. Data Understanding**

Dataset yang digunakan berasal dari Kaggle dan dapat diakses melalui tautan berikut:
[https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks](https://www.kaggle.com/datasets/jealousleopard/goodreadsbooks)

Setelah proses cleaning, data terdiri dari 11.119 buku dengan informasi sebagai berikut:

* `bookID`: ID unik untuk buku.
* `title`: Judul buku.
* `authors`: Nama penulis.
* `average_rating`: Rata-rata rating dari pengguna.
* `isbn`, `isbn13`: Nomor ISBN.
* `language_code`: Kode bahasa buku.
* `num_pages`: Jumlah halaman.
* `ratings_count`: Jumlah total rating.
* `text_reviews_count`: Jumlah ulasan teks.
* `publication_date`: Tanggal terbit.
* `publisher`: Nama penerbit.

### **EDA (Exploratory Data Analysis)**

Beberapa visualisasi dilakukan:

* Distribusi `average_rating` menunjukkan mayoritas buku memiliki rating antara 3,5 hingga 4,5.
* Distribusi `ratings_count` dan `text_reviews_count` berskala log karena distribusinya sangat miring.
* Bahasa dominan adalah bahasa Inggris (`en` dan `eng`).
* Penulis paling produktif dan penerbit terbanyak juga dianalisis.

## **4. Data Preparation**

Beberapa langkah data preparation dilakukan:

* **Menghapus kolom tidak relevan**: `bookID`, `isbn`, `isbn13`, `publication_date`, `publisher`, `num_pages`.
* **Menghapus data duplikat** dan **memastikan tidak ada missing values**.
* **Filter hanya buku berbahasa Inggris** (`en`, `eng`) untuk konsistensi.
* **Normalisasi rating**: hanya menyertakan buku dengan rating antara 0 dan 5.
* **Pembuatan kolom fitur gabungan (`combined_features`)** untuk kebutuhan TF-IDF.

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

## **5. Modeling and Result**

### **Pendekatan Content-Based Filtering**

Model menggunakan pendekatan:

* TF-IDF Vectorization untuk mengubah teks menjadi vektor.
* Cosine similarity untuk menghitung kemiripan antar buku.
* Fungsi `recommend_books(title)` untuk menghasilkan 10 buku rekomendasi teratas berdasarkan kemiripan konten.

**Contoh Output**
Rekomendasi untuk buku *The Hobbit*:

```
1. The Fellowship of the Ring - J.R.R. Tolkien  
2. The Two Towers - J.R.R. Tolkien  
3. The Return of the King - J.R.R. Tolkien  
... (dan seterusnya)
```

### **Kelebihan dan Kekurangan**

| Pendekatan              | Kelebihan                                         | Kekurangan                                                        |
| ----------------------- | ------------------------------------------------- | ----------------------------------------------------------------- |
| Content-Based Filtering | Tidak butuh data pengguna, cocok untuk cold-start | Rekomendasi cenderung sempit pada buku serupa, kurang eksploratif |
| Collaborative Filtering | Lebih personal, berdasarkan rating pengguna nyata | Membutuhkan data interaksi pengguna yang banyak dan berkualitas   |

## **6. Evaluation**

Karena pendekatan *content-based filtering* tidak memiliki label ground truth eksplisit, evaluasi dilakukan dengan cara:

* **Manual inspection** atas hasil rekomendasi.
* **Top-N recommendation** diverifikasi secara kualitatif: apakah hasil yang diberikan benar-benar mirip dan relevan?

Untuk proyek lanjutan, metrik seperti *precision\@k*, *recall\@k*, dan *Mean Average Precision* dapat digunakan jika data interaksi pengguna tersedia.
