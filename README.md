# Analisis_Food_Order
Proyek ini merupakan bagian dari pengembangan pribadi saya dalam memahami workflow analisis data. Pada proyek ini saya menggunakan bahasa pemrograman Python dengan beberapa library seperti Pandas, NumPy, Seaborn, dan Matplotlib.

## Keterangan Data
 Data [food_order.csv](food_order.csv) saya ambil dari website Kaggle pada data tersebut terdapat 9 kolom dan 1898 baris.

 Berikut adalah penjelasan untuk setiap kolom pada data ini:
 | Kolom                | Deskripsi                 |
|-----------------------|---------------------------|
| order_id              | Nomor Order               |
| customer_id           | Nomor Customer            |
| restaurant_name       | Nama Restoran             |
| cuisine_type          | Tipe Masakan              |
| cost_of_the_order	    | Biaya Pesanan             |
| day_of_the_week	    | Hari Dalam Minggu         |
| rating	            | Penilaian (1-5)           |
| food_preparation_time	| Waktu Makanan Disiapkan   |
| delivery_time         | Waktu Makanan Diantarkan  |

## Pengecekan Data
Langkah awal yang saya lakukan pada proses ini adalah melakukan pengecekan data, dimana saya melakukan pengecekan pada kolom di dataframe. Berikut kode yang digunakan:

### Cek Ringkasan
```
# Summary
df.info() 
```
![summary](pic/summary.png)

Dari gambar diatas, setelah dilakukan pengecekan untuk setiap kolom pada dataframe memiliki jumlah data yang bukan null sebanyak 1898 data, dan untuk tipe datanya terbagi menjadi 3 (int, float dan object).

```
#Check null values
print(df.isnull().sum())
```
![nullvalues](pic/check%20null%20value%20(2).png)

Dari gambar diatas, setelah dilakukan pengecekan tidak terdapat nilai null pada setiap kolom di dataframe.

---

### Cek Kolom
"Mengecek Kolom restaurant_name"
```
#Check restaurant name
print(df.restaurant_name.unique())
```
![restaurantname](pic/restaurant%20name%20(3).png)

Dari gambar diatas, terdapat 178 Nama restoran yang berada pada kolom restaurant_name dimana beberapa dari nama restoran tersebut terdapat special character.

Hal yang saya lakukan pertama adalah memeriksa nama-nama restoran yang menggunakan special character menggunakan kode berikut:
```
#menentukan spesial karakter
def contains_special_characters(text):
    pattern= r'[¤¾Ñ¼©@#$%^*()_+{}\[\]:;<>,?~\\]'
    return bool(re.search(pattern, text))

#mengecek restoran yang menggunakan spesial karakter
weird_restaurant = df[df['restaurant_name'].apply(contains_special_characters)]
print(weird_restaurant['restaurant_name'].unique())
```
![weird_name](pic/restaurant%20name%20mistake%20(4).png)

Terdapat 6 restoran yang menggunakan special character.

Selanjutnya setelah sudah menemukan restoran apa saja yang menggunakan special character, saya melakukan perubahan pada nama-nama restoran tersebut dengan kode berikut:
```
#Mengganti nama restoran lama dengan yang baru (Tanpa Special Character)
old_name = ['Big Wong Restaurant \x8c_¤¾Ñ¼','Empanada Mama (closed)',
            'Chipotle Mexican Grill $1.99 Delivery', "Joe's Shanghai \x8e_À\x8eü£¾÷´",
            'CafÌ© China', 'Dirty Bird To Go (archived)']

new_name = ['Big Wong Restaurant', 'Empanada Mama', 'Chipotle Mexican Grill', "Joe's Shanghai", 'Cafe China', 'Dirty Bird To Go']
print(new_name)

df['restaurant_name'] = df['restaurant_name'].replace(dict(zip(old_name, new_name)))
```
Berikut hasil setelah dilakukan pergantian nama restoran
![new_name](pic/restaurant%20name%20mistake%20new%20name%20(5).png)

---

"Mengecek Kolom cuisine_type"
```
print(df.cuisine_type.unique())
```
![cuisinetype](pic/cek%20cuisine%20(6).png)

Terdapat 14 jenis masakan pada kolom cuisine_type.

---

"Mengecek Kolom day_of_the_week"
```
print(df.day_of_the_week.unique())
```
![dayofweek](pic/cek%20day%20of%20week(7).png)

Pada kolom day_of_the_week hanya memiliki 2 data yaitu weekday dan weekend.

---

"Mengecek Kolom rating"
```
#Check rating
print(df.rating.value_counts(normalize=True) * 100)
```
![rating](pic/persentage%20rating%20(6).png)

Hasil dari pengecekan kolom rating, cukup banyak pesanan yang tidak diberikan ulasan atau penilaian yaitu sebesar 38,7%, dikarenakan hal tersebut saya akan membuat kolom baru dengan nama rating_new dimana untuk kolom yang berisi "Not given" akan saya ganti menjadi nilai null dengan kode berikut:

```
df['rating_new'] = df['rating'].replace('Not given', np.nan).astype(float)
df['rating_new'].isnull().sum()
```
![ratingnew](pic/add%20rating%20new%20(7).png)

Kolom rating_new sudah dibuat dimana untuk nilai "Not given" sudah diubah menjadi NaN (Not a Number).

---

"Membuat Kolom Waktu Penyelesaian Pesanan"
Setelah melihat kolom pada dataframe terdapat kolom food_preparation_time dan delivery_time, disini saya ingin membuat satu kolom baru bernama order_completion_time, dimana kolom ini berisi nilai dari jumlah waktu pada food_preparation_time dan delivery_time. Berikut kode untuk membuat kolom baru bernama order_completion_time:
```
df['order_completion_time'] = df['food_preparation_time'] + df['delivery_time']
print(df.head())
```
![completion_time](pic/order%20completed%20time%20(9).png)

---
"Mengecek Berapa Banyak Pesanan Dari Setiap Tipe Masakan dan Membuat Diagram Batang"
```
#Untuk mengecek jumlah order setiap tipe masakan
print(df.groupby("cuisine_type").size())

#Untuk mengecek persentase order setiap tipe masakan
print(df.cuisine_type.value_counts(normalize=True) * 100)

#Menampilkan diagram batang
df.cuisine_type.value_counts()
chart = df.cuisine_type.value_counts().plot(kind='bar')
plt.xticks(rotation = 45)
plt.show()

```
![Jml_order_per_cuisine](pic/order%20per%20type%20of%20cusine%20(10).png) ![prsn_order_per_cuisine](pic/persentage%20order%20per%20cuisine.png)

![bar_cuisine](pic/graphic%20bar%20cuisine_type%20(11).png)

Dari gambar diatas, masakan amerika menjadi masakan yang paling banyak dipesan (30,76%), masakan jepang menjadi masakan kedua yang paling banyak dipesan (24,76%) dan untuk masakan vietnam menjadi masakan paling sedikit dipesan (0,36%).

---

"Mengecek Kolom Biaya Pesanan"
```
#Melihat rata-rata biaya dari setiap 
df.cuisine_type.value_counts()
print(df.groupby('cuisine_type')['cost_of_the_order'].mean())

#Melihat desripsi dari kolom cost_of_the_order
print(df.cost_of_the_order.describe())
```
![mean_and_desc_cost](pic/mean%20and%20description%20cost.png)

Dari pengecekan yang dilakukan untuk rata-rata dari biaya pesanan adalah 16.49 dolar dengan biaya paling murah adalah 4.47 dolar dan biaya tertinggi 35.41 dolar.

---

"Mencari Hari Paling Sibuk (Paling Banyak Pemesanan)"
```
#Menjumlahkan pesanan pada kelompok hari
most_ordered_week = df.groupby('day_of_the_week').count()
most_ordered_week = most_ordered_week.reset_index('day_of_the_week')
print(most_ordered_week[['day_of_the_week', 'order_id']])

#Membuat dalam bentuk persentase
print(df.day_of_the_week.value_counts(1)*100)

#Membuat Diagram Batang 
most_ordered_week_plot = sns.barplot(x='day_of_the_week', y='order_id', data=most_ordered_week, palette=colors)
most_ordered_week_plot.set(xlabel= 'Day of the week', ylabel= 'Total Order')
plt.show()
```
![bussiest_day](pic/check%20bussiest%20dayofweek.png)
![bussiest_day_graph](pic/bussiest%20day%20graph(13).png)

Dari data diatas hari yang paling sibuk adalah weekend (71,18%) dengan jumlah pesanan sebanyak 1351 pesanan dan weekday (28.81) sebanyak 547 pesanan.

---
"Mengecek Kolom delivery_time"
```
print(df.delivery_time.describe())
```
![desc_delivery_time](pic/desc_delivery_time.png)

Dari pengecekan diatas untuk kolom delivery_time, rata-rata waktu pengiriman makanan adalah 24 menit dengan waktu paling cepatnya adalah 15 menit dan waktu paling lama adalah 33 menit.

---

"Mencari Waktu Rata-Rata Pengiriman Dari Setiap Jenis Masakan"
```
#Mencari rata-rata dari jenis masakan
average_delivery_time = df.groupby('cuisine_type')['delivery_time'].mean().reset_index('cuisine_type')
print(average_delivery_time)

#Membuat diagram
average_delivery_time_plot = sns.barplot(x='cuisine_type', y='delivery_time', data=average_delivery_time,palette=colors)
plt.setp(average_delivery_time_plot.get_xticklabels(), rotation=90)
average_delivery_time_plot.set(xlabel='Cuisine Type', ylabel='Delivery Time')
plt.show()
```
![mean_delivery_time](pic/mean%20time%20(14).png)
![grap_mean_delivery_time](pic/graphic%20time%20delivery%20(15).png)

Dari pengecekan diatas masakan korea memiki rata-rata pengiriman lebih cepat dibanding masakan lain yaitu sekitar 20 menit dan masakan vietnam memiliki rata-rata pengiriman terlama yaitu sekitar 26 menit. 

---
"Mencari Restoran Dengan Waktu Pengiriman Makanan Tercepat"
Pada proses ini saya ingin mencari 10 restoran dengan waktu pengiriman makanan tercepat. Berikut kode yang saya gunakan:
```
#Mencari restoran dengan waktu pengiriman makanan tercepat"
df_sorted = df.sort_values(by='delivery_time', ascending=True)[['restaurant_name','delivery_time']]
top_ten = df_sorted.head(10)
print(top_ten)

#Membuat diagramnya
plt.barh(top_ten['restaurant_name'], top_ten['delivery_time'])
plt.xlabel('Delivery Time (MINUTES)')
plt.ylabel('Restaurant Name')
plt.title('Top Ten Restaurants with Fastest Delivery Time')
plt.show()
```
![sort_delivery_time](pic/top_ten_deliv.png)
![graph_delivery_time](pic/graphic%20fastest%20delivery%20(16).png)

Dari hasil pencarian diatas, dapat disimpulkan untuk 10 restoran memiliki kesamaan pada waktu tercepat pengiriman makanan yaitu 15 menit.

---
"Mengecek Kolom food_preparation_time"
```
print(df.food_preparation_time.describe())
```
![desc_prep_time](pic/desc_food_prep_time.png)

Setelah dilakukan pengecekan pada kolom food_preparation_time, rata-rata waktu makanan disiapkan adalah 27 menit dengan waktu penyiapan tercepat yaitu 20 menit dan paling lama 35 menit.

---

"Mencari Restoran Dengan Waktu Persiapan Makanan Tercepat"
Sama seperti proses pada delivery_time, disini saya ingin membuat 10 besar restoran dengan waktu persiapan makanan tercepat. Untuk kodenya sebagai berikut:
```
#Menyortir 10 restauran dengan waktu persiapan tercepat
df_sorted = df.sort_values(by='food_preparation_time', ascending=True)[['restaurant_name','food_preparation_time']]
top_ten = df_sorted.head(10)
print(top_ten)

#Membuat diagram untuk 10 restauran dengan waktu persiapan tercepat
plt.barh(top_ten['restaurant_name'], top_ten['food_preparation_time'])
plt.xlabel('Preparation Time (MINUTES)')
plt.ylabel('Restaurant Name')
plt.title('Top Ten Restaurants with Fastest Preparation Time')
plt.show()
```
![sort_fast_prep](pic/top_ten_prep.png)
![graph_fast_prep](pic/graphic%20fastest%20preparation%20(17).png)

Dari hasil diatas dapat disimpulkan untuk waktu tercepat proses persiapan masakan adalah 20 menit dan ke 10 restoran tersebut memiliki waktu hidangan tercepat 20 menit.