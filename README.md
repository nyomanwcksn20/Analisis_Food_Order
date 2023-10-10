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
"Kolom restaurant_name"
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

"Kolom cuisine_type"
```
print(df.cuisine_type.unique())
```
![cuisinetype](pic/cek%20cuisine%20(6).png)

Terdapat 14 jenis masakan pada kolom cuisine_type.

---

"Kolom day_of_the_week"
```
#print(df.day_of_the_week.unique())
```
![dayofweek](pic/cek%20day%20of%20week(7).png)

Pada kolom day_of_the_week hanya memiliki 2 data yaitu weekday dan weekend.

---

"Kolom rating"
```
#Check rating
#print(df.rating.value_counts(normalize=True) * 100)
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