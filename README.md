# Jarkom-Modul-5-C07-2021

Kelompok C07

|      NRP       |                  Nama                   |
| :------------: | :-------------------------------------: |
| 05111940000046 |       Titian Pamungkas Anjasmara        |
| 05111940000134 |           Ahmad Lamaul Farid            |
| 05111940000150 | Jonathan Leonardo Hasiholan Simanjuntak |

## Topologi GNS3

Pertama - tama buat topologi pada GNS3 seperti pada gambar berikut :

![0.1](images/0.1.png)

Keterangan :

- DORIKI adalah DNS Server
- JIPANGU adalah DHCP Server
- MAINGATE dan JORGE adalah Web Server
- Jumlah Host pada BLUENO adalah 100 host
- Jumlah Host pada CIPHER adalah 700 host
- Jumlah Host pada ELENA adalah 300 host
- Jumlah Host pada FUKUROU adalah 200 host

## VLSM

Kemudian bagi topologi yang sudah diberikan ke dalam beberapa subnet kecil sesuai kebutuhan :

![0.2](images/0.2.png)

Tentukan jumlah IP yang diperlukan beserta dengan netmasknya untuk setiap subnet yang ada :

|  Subnet   | Jumlah IP | Netmask |
| :-------: | :-------: | :-----: |
|    A1     |     3     |   /29   |
|    A2     |    101    |   /25   |
|    A3     |    701    |   /22   |
|    A4     |     2     |   /30   |
|    A5     |     2     |   /30   |
|    A6     |    301    |   /23   |
|    A7     |    201    |   /24   |
|    A8     |     3     |   /29   |
| **Total** | **1314**  | **/21** |

Berdasarkan data tersebut, susun tree subnet VLSM seperti berikut :

![0.3](images/0.3.png)

Dengan demikian, didapatkan NID untuk masing - masing subnet sebagai berikut :

|  Subnet   | Jumlah IP | Netmask |      NID      |
| :-------: | :-------: | :-----: | :-----------: |
|    A1     |     3     |   /29   |  192.187.0.8  |
|    A2     |    101    |   /25   | 192.187.0.128 |
|    A3     |    701    |   /22   |  192.187.4.0  |
|    A4     |     2     |   /30   |  192.187.0.0  |
|    A5     |     2     |   /30   |  192.187.0.4  |
|    A6     |    301    |   /23   |  192.187.2.0  |
|    A7     |    201    |   /24   |  192.187.0.0  |
|    A8     |     3     |   /29   | 192.187.0.16  |
| **Total** | **1314**  | **/21** |

Masing - masing interface pada sebuah subnet dapat diberikan IP sesuai dengan aturan yang telah diberikan di atas.

- FOOSHA

  ![0.4](images/0.4.png)

- WATER7

  ![0.5](images/0.5.png)

- GUANHAO

  ![0.6](images/0.6.png)

- DORIKI

  ![0.7](images/0.7.png)

- JIPANGU

  ![0.8](images/0.8.png)

- JORGE

  ![0.9](images/0.9.png)

- MAINGATE

  ![0.10](images/0.10.png)

- BLUENO

  ![0.11](images/0.11.png)

- CIPHER

  ![0.12](images/0.12.png)

- ELENA

  ![0.13](images/0.13.png)

- FUKUROU

  ![0.14](images/0.14.png)

Setelah itu lakukan routing pada router **FOOSHA** sebagai berikut :

![0.15](images/0.15.png)

Kemudian pada **WATER7**, **FOOSHA**, **GUANHAO** install **DHCP Relay** dan lakukan perintah `apt-get update` pada ketiga node tersebut.

Setelah itu masukkan syntax `apt-get install isc-dhcp-relay -y`, setelah instalasi selesai, masukkan syntax `vi /etc/default/isc-dhcp-relay`. Sesuaikan `isc-dhcp-relay` seperti pada gambar berikut:

![0.16](images/0.16.png)

![0.17](images/0.17.png)

![0.18](images/0.18.png)

Kemudian lakukan perintah `service isc-dhcp-relay restart` pada 3 node diatas.

Pada **JIPANGU** install **DHCP Server** dan lakukan perintah `apt-get update` pada node tersebut.

Setelah itu masukkan syntax `apt-get install isc-dhcp-server -y`, setelah instalasi selesai, masukkan syntax `vi /etc/default/isc-dhcp-server`. Sesuaikan `isc-dhcp-server` seperti pada gambar berikut:

![0.19](images/0.19.png)

Edit file pada `vi /etc/dhcp/dhcpd.conf` dan lakukan konfigurasi seperti gambar :

![0.20](images/0.20.png)

![0.21](images/0.21.png)

Kemudian lakukan perintah `service isc-dhcp-server restart` pada node diatas.

Tambahkan configurasi pada interfaces setiap klien (BLUENO, CIPHER, ELENA dan FUKUROU):

```
auto eth0
iface eth0 inet dhcp
```

## Soal 1

```
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
```

### Jawaban

Masukkan perintah iptables berikut pada **FOOSHA**

```
iptables -t nat -A POSTROUTING -s 192.187.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.21
```

Untuk testingnya, lakukan `ping google.com` pada node lain. Misalkan disini kami melakukan testing pada node **BLUENO**

![0.22](images/0.22.png)

## Soal 2

```
Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
```

### Jawaban

Masukkan perintah iptables berikut pada **WATER7**

```
iptables -A FORWARD -d 192.187.0.8/29 -p tcp --dport 80 -j DROP
```

Untuk testingnya, lakukan perintah `nmap -p 80 192.187.0.10` pada client. Disini kami menggunakan **BLUENO**

![0.23](images/0.23.png)

Kalau nanti outputnya port 80 `filtered` berarti berhasil `iptables`-nya.

## Soal 3

```
Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
```

### Jawaban

Masukkan perintah iptables berikut pada **Doriki dan Jipangu**

```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

### Testing

Lakukan PING pada semua host yang ada secara bersamaan. Host terakhir yang dilakukan ping tidak akan bisa.

### Blueno

![31](https://user-images.githubusercontent.com/77373958/145673429-ee65221c-5b74-4b0e-8f36-a42c623518f0.PNG)

### Fukuro

![33](https://user-images.githubusercontent.com/77373958/145673438-00e61e77-280a-4c44-bf7d-e8e3ed7e1840.PNG)

### Elena 

![32](https://user-images.githubusercontent.com/77373958/145673435-178059e8-0113-4767-b4d5-2af12522bf12.PNG)

### Ciper

![34](https://user-images.githubusercontent.com/77373958/145673439-8e78dc0a-6837-4d77-aa46-a873f979ffaf.PNG)

## Soal 4

```
Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.
```

Masukkan perintah iptables berikut ke **Doriki**

```
iptables -A INPUT -s 192.187.0.128/25 -m time --timestart 07:00 --timestop 1
iptables -A INPUT -s 192.187.0.128/25 -j REJECT
```

```
iptables -A INPUT -s 192.187.4.0/22 -m time --timestart 07:00 --timestop 15:
iptables -A INPUT -s 192.187.4.0/22 -j REJECT
```

### Testing

Testing dilakukan pada host Blueno dan Ciper dengan melakukan ping ke Doriki, hasilnya tidak akan bisa.

![41](https://user-images.githubusercontent.com/77373958/145673587-4ea5cd16-f43b-4339-9e11-2e928ecd67d8.PNG)

![42](https://user-images.githubusercontent.com/77373958/145673589-ead7a111-a476-4849-8d3f-de92a9c76086.PNG)

## Soal 5

```
Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.
```

### Jawaban

Masukkan perintah - perintah iptables berikut pada **Doriki**

```
iptables -A INPUT -s 192.187.2.0/23 -m time --timestart 15:01 --timestop 23:59 -j ACCEPT
iptables -A INPUT -s 192.187.2.0/23 -m time --timestart 00:00 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 192.187.2.0/23 -j REJECT
```

### Testing

- Pada salah satu node client yang ada pada soal, pertama - tama periksa dahulu waktu sistem saat ini. Node yang dipilih kali ini adalah Elena.

  ```
  date
  ```

  ![5.1](images/5.1.PNG)

- Terlihat bahwa waktu saat ini masuk kedalam jam kerja node Doriki untuk subnet Elena (15:01 - 23:59), sehingga ping berhasil dilakukan. Untuk melakukan ping ke node Doriki, jalankan perintah berikut.

  ```
  ping 192.187.0.10
  ```

  ![5.2](images/5.2.PNG)

- Untuk mengetes lebih lanjut, ubah waktu sistem saat ini menjadi diluar waktu kerja node Doriki dengan perintah berikut.

  ```
  date -s "Mon Dec  6 08:36:18 UTC 2021"
  ```

  ![5.3](images/5.3.PNG)

- Setelah itu, lakukan ping kembali ke node Doriki. Terlihat bahwa Doriki tidak dapat diakses oleh subnet Elena. Artinya, aturan iptables yang diterapkan sudah sukses.

  ![5.4](images/5.4.PNG)

# Soal 6

```
Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate.
```

### Jawaban

Masukkan perintah - perintah iptables berikut pada **Guanhao**

```
iptables -A PREROUTING -t nat -d 192.187.0.10 -p tcp -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.187.0.18
iptables -A PREROUTING -t nat -d 192.187.0.10 -p tcp -j DNAT --to-destination 192.187.0.19
```

### Testing

- Pertama - tama, pada kedua node web server, yaitu pada _Jipangu_ dan _Maingate_, install service apache dengan menjalankan perintah berikut.

  ```
    apt-get update
    apt-get install apache2 -y
  ```

- Setelah berhasil diinstall, edit file _/var/www/html/index.html_ pada kedua node menjadi seperti pada gamber berikut. (Sesuaikan nama node-nya).

  - Maingate

  ![6.1](images/6.1.PNG)

  - Jorge

  ![6.2](images/6.2.PNG)

- Kemudian restart service apache pada kedua node web server dengan menggunakan perintah :

  ```
  service apache2 restart
  ```

- Setelah kedua web server berhasil disiapkan, coba akses ke node Doriki (DNS Server) lewat salah satu node yang berhubungan langsung dengan router **Guanhao** (pada kasus ini digunakan node Elena) dengan menggunakan perintah :

  ```
  curl 192.187.0.10
  ```

  ![6.3](images/6.3.PNG)

- Terlihat pada percobaan pertama akses ke Doriki (DNS Server), paket dialihkan ke node Web Server Jorge. Coba akses lagi ke node Doriki (DNS Server) dengan menggunakan perintah yang sama dengan yang diatas.

  ![6.4](images/6.4.PNG)

- Terlihat bahwa akses ke Doriki sekarang dialihkan ke node Web Server Maingate. Jika dicoba terus menerus akses ke Doriki, maka paket akan terus menerus dialihkan secara bergantian ke Jorge atau Maingate. Dengan begitu, artinya konfigurasi yang diinginkan telah berhasil diterapkan.

  ![6.5](images/6.5.PNG)
