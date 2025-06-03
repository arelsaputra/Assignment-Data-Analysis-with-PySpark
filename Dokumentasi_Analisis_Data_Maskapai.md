# Dokumentasi Analisis Data Aktivitas Pelanggan Maskapai Penerbangan

## 1. Pendahuluan

Dokumentasi ini menjelaskan langkah-langkah analisis data aktivitas pelanggan maskapai penerbangan menggunakan PySpark. Dataset terdiri dari tiga tabel utama:

- `calendar`
- `customer_flight_activity`
- `customer_loyalty_history`

Tujuan analisis adalah memahami pola penerbangan, loyalitas pelanggan, dan hubungan antara karakteristik pelanggan dengan aktivitas penerbangan mereka.

---

## 2. Langkah-Langkah Analisis

### 2.1 Ekstrak Data ke dalam DataFrame

- Memuat dataset CSV ke dalam PySpark DataFrame.
- Memeriksa dan menyesuaikan tipe data setiap kolom agar sesuai kebutuhan analisis.

### 2.2 Cleaning Data

- Menghapus baris dengan nilai kosong di kolom penting (`loyalty_number`, `year`, `total_flights`).
- Mengisi nilai kosong pada kolom kategorikal (`education`, `loyalty_card`) dengan label `Unknown`.
- Menghapus data duplikat.

### 2.3 Transformasi Data

- Melakukan join antar tabel `customer_flight_activity` dan `customer_loyalty_history` berdasarkan `loyalty_number`.
- Membuat temporary view bernama `customer_data` dari hasil join untuk menjalankan query SQL.

### 2.4 Analisis Data Menggunakan PySpark SQL

**Query 1:** Rata-rata jumlah penerbangan per pelanggan dalam setahun

````sql
SELECT year, AVG(total_flights) AS avg_flights_per_customer
FROM customer_data
GROUP BY year
ORDER BY year
````

**Query 2:** Distribusi rata-rata loyalty points berdasarkan status kartu loyalitas
```sql
SELECT loyalty_card, AVG(points_accumulated) AS avg_points
FROM customer_data
GROUP BY loyalty_card
ORDER BY avg_points DESC
````

**Query 3:** Hubungan tingkat pendidikan pelanggan dengan rata-rata jumlah penerbangan

```sql
SELECT education, AVG(total_flights) AS avg_flights
FROM customer_data
GROUP BY education
ORDER BY avg_flights DESC
```

**Query 4:** Tren jumlah penerbangan dari waktu ke waktu

```sql
SELECT year, SUM(total_flights) AS total_flights
FROM customer_data
GROUP BY year
ORDER BY year
```

## 3. Visualisasi Data

Visualisasi dibuat menggunakan Matplotlib dan Seaborn untuk memperjelas hasil analisis, antara lain:

- Grafik garis rata-rata penerbangan per pelanggan per tahun.
- Grafik batang rata-rata loyalty points berdasarkan status kartu loyalitas.
- Violin plot distribusi penerbangan berdasarkan tingkat pendidikan.
- Grafik garis tren rata-rata jarak penerbangan per tahun.

## 4. Penyimpanan Hasil Analisis

Hasil query disimpan dalam format CSV di folder output/ menggunakan PySpark .write dengan contoh kode:

```python
avg_flights.write.mode("overwrite").csv("output/avg_flights_per_year.csv", header=True)
loyalty_points_dist.write.mode("overwrite").csv("output/avg_points_loyalty_card.csv", header=True)
education_flights.write.mode("overwrite").csv("output/avg_flights_by_education.csv", header=True)
flights_trend.write.mode("overwrite").csv("output/total_flights_trend.csv", header=True)
```

## 5. Interpretasi Insight

- Rata-rata penerbangan per pelanggan mengalami fluktuasi tahunan yang menunjukkan pola perilaku dan kondisi pasar.
- Pelanggan dengan status kartu loyalitas yang lebih tinggi umumnya mengumpulkan poin lebih banyak.
- Tingkat pendidikan pelanggan memengaruhi frekuensi penerbangan, dengan kelompok pendidikan lebih tinggi cenderung lebih aktif.
- Total penerbangan tahunan dapat digunakan sebagai indikator volume bisnis dan tren industri.

## 6. Struktur Folder

- visualizations/ — menyimpan hasil gambar grafik dalam format PNG.
- output/ — menyimpan hasil ekspor data analisis dalam format CSV.

## 7. Referensi

- PySpark Documentation (https://spark.apache.org/docs/latest/api/python/)
- Matplotlib Documentation (https://matplotlib.org/stable/contents.html)
- Seaborn Documentation (https://seaborn.pydata.org/)
