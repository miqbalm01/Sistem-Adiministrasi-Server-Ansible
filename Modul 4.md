# Modul 4 - Web Server, Load Balancing dan uji performansi


#### Grahito Ardani B 1202199006 || M Iqbal Maulana 1202190023

#### Teknologi Informasi 02-02

------


<br>

### Container APP (PHP 5)

Pertama, kita lakukan terlebih dahulu salin container debian_php5.6 untuk menjaga backup container. setelah itu jalankan container salinan tersebut.

```
lxc-copy -n debian_php5.6 -N debian_php5.6_2 -sKD		 // clone container
lxc-copy -n debian_php5.6 -N debian_php5.6_3 -sKD
sudo lxc-start -n debian_php5.6_2	          // Jalankan Container
sudo lxc-start -n debian_php5.6_3
Sudo lxc-ls -f										// Menampilkan Container		
```

 ![1](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/app/1.PNG)

<br>

Pada Container debian_php5.6_2 lakukan configurasi IP

![2.1](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/app/2.1.PNG)


<br>

Pada Container debian_php5.6_3 lakukan configurasi IP

![2](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/app/2.PNG)

<br>

Setelah itu, lakukan konfigurasi pada hosts. disini kita tambahkan domain serta nama domain container kita.

```
nano /etc/hosts
```

 ![3](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/app/3.PNG)

<br>

Lalu masuk directory sites-avalaible, di folder tersebut kita lakukan edit domain file tersebut dengan mengganti name servernya. jangan lupa restart containernya

```
 nano /etc/nginx/sites-available/lxc_php5.dev
```

![4](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/app/4.PNG)

<br>

### Container BLOG (PHP 7)

Sekarang ganti kita lakukan salin container ubuntu_php7.4. setelah itu jalankan container salinan tersebut.

```
lxc-copy -n ubuntu_php7.4 -N ubuntu_php7.4_2 -sKD		 // clone container
lxc-copy -n ubuntu_php7.4 -N ubuntu_php7.4_3 -sKD
sudo lxc-start -n ubuntu_php7.4_2						// Jalankan Container
sudo lxc-start -n ubuntu_php7.4_3
Sudo lxc-ls -f										   // Menampilkan Container	
```



Pada Container ubuntu_php7.4_2 lakukan configurasi IP

![2.1](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/blog/2.1.PNG)

<br>

Pada Container ubuntu_php7.4_3 lakukan configurasi IP

![2](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/blog/2.PNG)

<br>

Setelah itu, lakukan konfigurasi pada hosts. disini kita tambahkan domain serta nama domain container kita.

```
nano /etc/hosts
```

![3](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/blog/3.PNG)

<br>


Lalu masuk directory sites-avalaible, di folder tersebut kita lakukan edit domain file tersebut dengan mengganti name servernya. jangan lupa restart containernya

```
 nano /etc/nginx/sites-available/lxc_php7.dev
 exit											// Keluar dari Container
```

![4](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/blog/4.PNG)

<br>
<br>

### VM Utama

Pada VM Utama, Daftarkan semua container yang telah kita buat di hosts (Pada VM Utama), jangan lupa lakukan restart vm

```
Sudo Nano /etc/hosts
```

![1](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/1.PNG)

<br>


Lalu kita buka dan jalankan Software Jmeter, Ubah jumlah pengguna dari akses pengguna pada masing-masing user (50, 100, 150)

- #### 50 User

Tambahkan banyak User sejumlah 50 User untuk melakukan akses Website

![2](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/2.PNG)

<br>

Grafik didapatkan pada saat diakses 50 Users

![3](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/3.PNG)

<br>

Detail User yang mengakses Website

![4](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/4.PNG)

<br>

Detail website yang di akses 50 Users secara bersamaan dimasing-masing halaman landing, blog serta app

![5](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/5.PNG)

<br>

- #### 100 User

Tambahkan banyak User sejumlah 100 User untuk melakukan akses Website

![6](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/6.PNG)

<br>


Grafik didapatkan pada saat diakses 100 Users

![7](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/7.PNG)

<br>

Detail User yang mengakses Website

![8](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/8.PNG)

<br>

Detail website yang di akses 100 Users secara bersamaan dimasing-masing halaman landing, blog serta app

![9](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/9.PNG)

<br>


- #### 150 User

Tambahkan banyak User sejumlah 150 User untuk melakukan akses Website

![10](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/10.PNG)

<br>

Grafik didapatkan pada saat diakses 150 Users

![11](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/11.PNG)

<br>


Detail User yang mengakses Website

![12](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/12.PNG)

<br>


Detail website yang di akses 150 Users secara bersamaan dimasing-masing halaman landing, blog serta app

![13](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/13.PNG)

<br>


Lalu balik ke dalam VM kita, masuk kedalam directory file vm.local yang terletak di sites-available. tambhakan upstream pada masing-masing container (landing, php5, dan php7), lalu didalam container tersebut kita lakukan pemanggilan file domain.  

```
 nano /etc/nginx/sites-available/vm.local
```

![14](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/14.PNG)

<br>


rubah juga pada bagian url proxy_pass

![15](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/15.PNG)

<br>


Setelah melakukan perubahan pada vm kita, jangan lupa merestart vm dan buka lagi software Jmeternya. lalu kita analisa seteleh kita lakukan konfigurasi ulang dengan menambahkan load balacing upstream, apakah terdapat perubahan??

- #### 50 User menggunakan  Load Balacing  Upstream

Tambahkan banyak User sejumlah 50 User untuk melakukan akses Website  

![16](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/16.PNG)

<br>


Grafik didapatkan pada saat diakses 50 Users

![17](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/17.PNG)

<br>


Detail User yang mengakses Website

![18](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/18.PNG)

<br>

Detail website yang di akses 50 Users secara bersamaan dimasing-masing halaman landing, blog serta app

![19](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/19.PNG)

<br>


- #### 100 User menggunakan Load Balacing Upstream

Tambahkan banyak User sejumlah 100 User untuk melakukan akses Website  

![20](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/20.PNG)

<br>

Grafik didapatkan pada saat diakses 100 Users

![21](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/21.PNG)

<br>


Detail User yang mengakses Website

![22](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/22.PNG)

<br>


Detail website yang di akses 100 Users secara bersamaan dimasing-masing halaman landing, blog serta app

![23](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/23.PNG)

<br>


- #### 150 User menggunakan Load Balacing Upstream

Tambahkan banyak User sejumlah 150 User untuk melakukan akses Website  

![24](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/24.PNG)

<br>

Grafik didapatkan pada saat diakses 150 Users	

![25](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/25.PNG)

<br>

Detail User yang mengakses Website

![26](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/26.PNG)

<br>


Detail website yang di akses 150 Users secara bersamaan dimasing-masing halaman landing, blog serta app

![27](https://github.com/IceLake11/Kelompok11-sas02/blob/main/assets/Modul4/27.PNG)

<br>

#### <u>Analisa hasil Software Jmeter</u>

#### Rata-Rata Waktu Pengguna Akses Web

##### 50 Pengguna

Ketika terdapat 50 Pengguna, Rata-Rata Waktu yang butuhkan pengguna dalam mengakses masing-masing halaman.

Menggunakan Load Balance
- Landing (50 Pengguna) : 60 mdtk / 0,006 detik
- Blog (50 Pengguna)	: 36 mdtk / 0,036 detik	
- App (50 Pengguna)	: 45 mdtk / 0,045 detik	
Rata-Rata Keseluruhan Halaman(150 Pengguna) : 150 mdtk / 0,150 detik 

Tanpa Load Balance
- Landing (50 Pengguna)	: 51 mdtk / 0,019 detik
- Blog (50 Pengguna)	: 3934 mdtk / 3,9 detik 
- App (50 Pengguna)	: 2 mdtk / 0,002 detik
Rata-Rata Keseluruhan Halaman(150 Pengguna) : 1329 mdtk / 1,3 detik 

##### 100 Pengguna

Ketika terdapat 100 Pengguna, Rata-Rata Waktu yang butuhkan pengguna dalam mengakses masing-masing halaman.

Menggunakan Load Balance
- Landing (100 Pengguna): 129 mdtk / 0,129 detik
- Blog (100 Pengguna)	: 23 mdtk / 0,023 detik	
- App (100 Pengguna)	: 26 mdtk / 0,026 detik	
Rata-Rata Keseluruhan Halaman(300 Pengguna) : 59 mdtk / 0,059 detik 

Tanpa Load Balance
- Landing (100 Pengguna): 86 mdtk / 0,086 detik
- Blog (100 Pengguna)	: 3623 mdtk / 3,6 detik 
- App (100 Pengguna)	: 2 mdtk / 0,002 detik
Rata-Rata Keseluruhan Halaman(300 Pengguna) : 1237 mdtk / 1,2 detik 

##### 150 Pengguna

Ketika terdapat 150 Pengguna, Rata-Rata Waktu yang butuhkan pengguna dalam mengakses masing-masing halaman.

Menggunakan Load Balance
- Landing (150 Pengguna): 117 mdtk / 0,117 detik
- Blog (150 Pengguna)	: 58 mdtk / 0,058 detik	
- App (150 Pengguna)	: 60 mdtk / 0,060 detik	
Rata-Rata Keseluruhan Halaman(450 Pengguna) : 78 mdtk / 0,078 detik 

Tanpa Load Balance
- Landing (150 Pengguna): 120 mdtk / 0,120 detik
- Blog (150 Pengguna)	: 4429 mdtk / 4,4 detik	
- App (150 Pengguna)	: 2 mdtk / 0,02 detik	
Rata-Rata Keseluruhan Halaman(450 Pengguna) : 1517 mdtk / 1,5 detik 

Dari Jumalh Pengguna(50,100,150), Disini kita dapat mengetahui bahwa rata-rata waktu pengguna mengakses web kita lebih cepat (Menggunakan Load balancer) dibandingkan jika kita tidak menggunakan load balancer. 

<br>

#### Throughput (kecepatan/rate transfer data efektif, yang diukur dalam bps).

##### 50 Pengguna

Ketika terdapat 50 Pengguna, melakukan transfer data dalam masing-masing halaman.

Menggunakan Load Balance
- Landing (50 Pengguna) : 264,6/detik
- Blog (50 Pengguna)	: 264,6/detik
- App (50 Pengguna)	: 434,8/detik	
Total Throughput Keseluruhan Halaman(150 Pengguna) : 738,9/detik 

Tanpa Load Balance
- Landing (50 Pengguna) : 694,4/detik
- Blog (50 Pengguna)	: 10,1/detik
- App (50 Pengguna)	: 24,2/detik	
Total Throughput Keseluruhan Halaman(150 Pengguna) : 30,2/detik 

##### 100 Pengguna

Ketika terdapat 100 Pengguna, melakukan transfer data dalam masing-masing halaman.

Menggunakan Load Balance
- Landing (100 Pengguna) : 2,7/detik
- Blog (100 Pengguna)	: 2,7/detik
- App (100 Pengguna)	: 2,7/detik	
Total Throughput Keseluruhan Halaman(300 Pengguna) : 8,2/detik 

Tanpa Load Balance
- Landing (100 Pengguna) : 48,4/menit
- Blog (100 Pengguna)	: 47,1/menit
- App (100 Pengguna)	: 47,8/menit
Total Throughput Keseluruhan Halaman(300 Pengguna) : 2,4/detik

##### 150 Pengguna

Ketika terdapat 150 Pengguna, melakukan transfer data dalam masing-masing halaman.

Menggunakan Load Balance
- Landing (150 Pengguna) : 2,4/detik
- Blog (150 Pengguna)	: 2,4/detik
- App (150 Pengguna)	: 2,4/detik	
Total Throughput Keseluruhan Halaman(450 Pengguna) : 7,1/detik 

Tanpa Load Balance
- Landing (150 Pengguna) : 1,2/detik
- Blog (150 Pengguna)	: 1,1/detik
- App (150 Pengguna)	: 1,2/detik
Total Throughput Keseluruhan Halaman(450 Pengguna) : 3,4/detik

Dari Jumlah Pengguna(50,100,150), Disini kita dapat mengetahui bahwa kecepatan tranfers data pada web kita lebih besar(Menggunakan Load balancer) dibandingkan jika kita tidak menggunakan load balancer. 
