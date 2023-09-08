## Laporan Hasil Praktikum Sistem Adiministrasi server

------

#### Laporan Praktikum 1

Kelompok 11[IT 02-02]

Muhammad Iqbal Maulana (1202190023) || Grahito Ardani Bimanta (1202199006)

------

#### Langkah-langkah

1. Rename ubuntu_php5.6 menjadi ubuntu_landing, serta rubah IP mengikuti skema yang baru!!

   * cek status kontainer apakah masih  berjalan jika ya, maka kita harus menonaktifkan terlebih dahulu untuk memproses penggantian nama pada kontainer tersebut

   

   ```markdown
   sudo lxc-stop -n ubuntu_php5.6
   ```

   ![1](https://user-images.githubusercontent.com/93030868/138581366-56e46222-194f-4b82-93cc-4c862c105cfc.png)

   * setelah konatiner telah nonaktif maka kita dapat mengganti nama kontainer nya 

     ```markdown
     sudo lxc-copy -R -n ubuntu5.6 -N ubuntu_landing
     ```

   * kita dapat melihat nama apakah telah terganti

     ```markdown
     sudo lxc-ls -f
     ```

   ![2](https://user-images.githubusercontent.com/93030868/138581385-3f5c33bb-e0e8-479a-a108-2cc994eb2a41.png)

  2.  Install Lxc debian 9 dengan nama debian_php5.6

      Lakukan instalasi debian 9 yang tersedia pada template kontainer ubutnu untuk memporses php5.6

      Debian : Jenis Kontainer

      Stretch : versi Kontainer

      ```markdown
      Sudo-create -n Debian_php5.6 -t download -- --dist Debian –release stretch -h amd64 –force-cache –no-validate –server images.linuxcontainers.org
      ```

   ![3](https://user-images.githubusercontent.com/93030868/138581396-1511e213-15d4-4adc-b679-329875cc1f15.PNG)

   3. Setup nginx pada debian_php5.6 untuk domain http://lxc_php5.dev , buat halaman index.html yang menerangkan informasi nama lxc

   * Jalankan lxc debian 5 dan lakukan installasi nginx

   ```markdown
   sudo lxc_start -n debian_php5.6
   sudo lxc-attach -n debian_5.6
   sudo apt install nginx nginx-extras
   ```
![4](https://user-images.githubusercontent.com/93030868/138581410-9846af09-9e70-4c3a-8d10-d1464e7095be.png)

   * Install net-tools curl yang berfungsi untuk Untuk memantau aktivitas jaringan komputer. Untuk melihat konektivitas dari suatu perangkat jaringan komputer dengan yang lain. Untuk melihat data atau file apa yang sedang ditransfer

     ```markdown
     apt install net-tools curl
     ```

     ![5](https://user-images.githubusercontent.com/93030868/138581423-b8131e6f-bf74-4cd0-a78e-8f3a9f7db6c1.PNG)

   * Lakukan cek ip untuk mengetahui ip default yang dipakai, Lalu masuk dalam folder/ etc/network untuk melakukan Settings ip static

     ```
     ipconfig
     nano /etc/network/interfaces
     ```

     ![6](https://user-images.githubusercontent.com/93030868/138581453-0ba298f6-c664-4426-a5ac-48d2efb31fea.png)

     

   * Lakukan setting ip sesuai dengan kebutuhan, lalu restart jaringan tersebut agar sistem pada jaringan dapat memperbarui ip yang baru dilakukan konfigurasi

     ```markdown
     systemctl restart networking.service
     ```

     ![7](https://user-images.githubusercontent.com/93030868/138581464-28b3401b-dbe0-4d13-86aa-20e53b98c0c6.png)

   * cek ip apakah telah terganti. ternyata tidak berganti, hal ini kemungkinan setting ip gagal. jadi kita melakukan shutdown untuk merefresh sistem yang telah di konfigurasi ulang

     ```markdown
     shutdown now
     ```

     ![8](https://user-images.githubusercontent.com/93030868/138581468-5d3b3440-fe5f-4a05-8c53-fce0783c0871.png)

   * jalankan ulang lxc debian 9 nya dan coba ip apakah sudah berganti. ip telah terganti untuk memastikan kontainer lxc terhubung ke internet dengan melakukan ping ke google

     ```markdown
     ping google.com
     ```

     ![9](https://user-images.githubusercontent.com/93030868/138581480-e430a088-0539-4f3c-8d60-d3a1c05fc151.PNG)

   * setelah itu masuk ke folder sites avaible yang merupakan domain file nya dan membuat file dev

     ```markdown
     cd /etc/nginx/sites-avaible
     touch lxc-php5.dev
     ```

     ![10](https://user-images.githubusercontent.com/93030868/138581508-fd296205-fcf5-460d-9691-f6c4da86fab2.png)

     

   * melakukan konfigurasi pada file lxc_php5.dev
   
     ```
     nano lxc_php5/dev
     ```

     ![11](https://user-images.githubusercontent.com/93030868/138581556-89a4a475-f56d-44da-b0d5-2518a0623682.png)

   * masuk ke folder sites-enabled dan melakukan penyambungan atau *lean* yang berfungsi untuk membuat tautan antar file dan cek apakah syntax file yang dibuat sudah benar dan menjalankan ulang nginx
   
     ```
     cd ../sites-enabled
     ln-s /etc/nginx/sites-available/lxc_php5.dev
     nginx -t
     nginx -s reload
     ```

     ![12](https://user-images.githubusercontent.com/93030868/138581600-18a61ba1-0133-4fb6-8699-d434b1b5cebd.png)

   * masuk ke host untuk melakukan konfigurasi menghubungkan lxc dengan domain yang telah di buat
   
     ```markdown
     nano /etc/host
     ```

     ![13](https://user-images.githubusercontent.com/93030868/138581612-e1f75b25-28ce-4a53-bafe-68769ed51c19.png)

   * konfigurasi host pada ubuntu5.6, tambahkan ip dan nama file agar kedua nya akan saling terhubung

     ![14](https://user-images.githubusercontent.com/93030868/138581618-36ffa489-aa91-4e13-852e-5e51f6cedd89.png)

   * masuk ke folder var/www/html lalu membuat sebuah folder yang nantinya akan diakses di browser. membuat file yang mengcopy dari file *index.nginx-debian.html*menjadi file baru *index.html*
   
     ```markdown
     cd var/www/html
     mkdir app
     cp index.nginx-debian.html_php5.6/index.html
     nano index.html
     ```

     ![15](https://user-images.githubusercontent.com/93030868/138581643-5cb23de3-6f6c-40a0-bf77-c173507de0af.png)

   * editing file index yang nantinya ditampilkan pada browser

     ![16](https://user-images.githubusercontent.com/93030868/138581648-54467a28-cd5c-4745-aded-fa92cfc3f664.png)

   * memanggil domain yang sudah dibuat untuk memastikan container berjalan dengan baik yang dimana akan menampilkan file yang akan muncul pad web
   
     ```markdown
     curl -i http://lxc_php.dev
     ```

     ![17](https://user-images.githubusercontent.com/93030868/138581662-b3f993b7-776b-4499-bafe-49888235375a.png)

   Ubuntu php 7

   * melakukan konfigurasi pada ubuntu php 7. berhubung sudah melakukan instalasi sebelumnya maka kita hanya menjalankan ubuntu php7
   
     ```markdown
     sudo lxc-attach -n ubuntu_php7.4
     sudo apt install nginx nginx-extras
     apt install net-tools curl
     ```

     ![18](https://user-images.githubusercontent.com/93030868/138581699-ca06a032-f043-4cf8-92ce-af227f3e1b3a.png)

   * konfigurasi ip menjadi ip statis
   
     ```markdown
     nano /etc/netplan/10-lxc.yaml
     ```

     ![19](https://user-images.githubusercontent.com/93030868/138581708-8f6465c5-c990-4a4c-ae20-f0a74eb34958.png)

   * restart sistem karena kita telah merubah konfigurasi ip. kita melakukan cek ip untuk memastikan ip telah terganti dan melakukan cek apakah terhubung dengan internet pada jaringan php 7 dapat berjalan
   
     ```markdown
     sudo netplan apply
     ifconfig
     ping google.com
     ```

     ![20](https://user-images.githubusercontent.com/93030868/138581716-e2fb2a5e-f26b-412c-9731-fe73164113c6.png)

   * masuk pada folder /etc/nginx/sites-available dan membuat file dev. 
   
     ```markdown
     cd /etc/nginx/sites-available
     touch lxc-php5.dev
     ```

     ![21](https://user-images.githubusercontent.com/93030868/138581730-74cfc37e-76d3-4f7b-adbc-7d61e5f17ec2.png)

   * setting konfigurasi pada nginx php7
   
     ```markdown
     nano lxc_php7.dev
     ```

     ![22](https://user-images.githubusercontent.com/93030868/138581739-c661788d-577c-44ec-81e3-48dc23dff438.png)

   * masuk ke folder sites-enabled dan melakukan penyambungan atau *lean* yang berfungsi untuk membuat tautan antar file dan cek apakah syntax file yang dibuat sudah benar dan menjalankan ulang nginx
   
     ```markdown
     cd ../sites-enabled
     ln -s /etc/nginx/sites-available/lxc_php7.dev
     nginx -t
     nginx -s reload
     ```

     ![23](https://user-images.githubusercontent.com/93030868/138581751-6bffefa3-9287-49e4-98fc-65fd84c1dbac.png)

   * konfigurasi host pada php7, tambahkan ip dan nama file agar keduanya saling terhubung
   
     ```markdown
     nano /etc/hosts
     ```
     ![24](https://user-images.githubusercontent.com/93030868/138581761-96dc6100-b405-46e2-a29e-c6aaef8a8910.png)

   * masuk ke folder var/www/html lalu membuat sebuah folder yang nantinya akan diakses di browser. membuat file yang mengcopy dari file *index.nginx-debian.html* menjadi file baru *index.html*
   
     ```markdown
     cd /var/www/html
     mkdir blog
     cd index.nginx-debian.html blog/index.html
     nano index.html
     ```

     ![25](https://user-images.githubusercontent.com/93030868/138581781-5be93f9b-3302-43b5-8c52-d1e971244003.png)

   * editing pada file blog/index.html dan beri menu untuk menuju ke halaman lain

     ![26](https://user-images.githubusercontent.com/93030868/138581786-f35c575f-86c3-4ff3-aaf7-3d49f0bcb672.png)

   * memanggil domain yang telah dibuat untuk memastikan file index yang kita buat dapat berjalan dengan baik dalam kontainer ubuntu 7.4
   
     ```markdown
     curl -i http://lxc-php7.dev
     ```

     ![27](https://user-images.githubusercontent.com/93030868/138581798-145629c3-4eaf-479c-9547-e34d972e6465.png)

   4.  Setup nginx pada ubuntu_landing untuk domain http://lxc_landing.dev , buat halaman index.html yang menerangkan informasi nama lxc	

   * masuk pada kontainer ubuntu_landing lalu cek ip apakah terdapat ip yang sama jika ya, kita dapat mengkonfigurasi ulang karena dapat menimbulkan conflik atau tabrakan antar ip
   
     ```markdown
     sudo lxc-ls -f
     sudo lxc-attach -n ubuntu_landing
     ```

     ![28](https://user-images.githubusercontent.com/93030868/138581810-29ec706b-a856-45b6-8194-4827b95fdfc0.png)


   * install net-tools pada ubuntu landing
   
     ```markdown
     apt install nano net-tools curl
     ```

     ![29](https://user-images.githubusercontent.com/93030868/138581824-034f434e-ffad-45c9-98a0-d70cd582afc7.png)

   * konfigurasi ip yang sebelumnya 10.0.3.10 menjadi 10.0.3.103
   
     ```markdown
     nano /etc/network/interfaces
     ```

     ![30](https://user-images.githubusercontent.com/93030868/138581838-d2e8bb65-5d0e-4ed7-9739-f2cd3cc41426.png)

   * cek ip dan cek jaringan untuk memastikan terhubung ke internet
   
     ```markdown
     ifconfig
     ping google.com
     ```

     ![31](https://user-images.githubusercontent.com/93030868/138581850-c6ca1daa-b29e-472b-b6d3-f63384acc94f.png)

   * membuat file domain dengan cara masuk ke folder sites-available. karena kita sudah memiliki file domain pada folder tersebut maka kita dapat melakukan rename file menjadi lxc_landing.dev 
   
     ```markdown
     cd /etc/nginx/sites-available
     sudo mv lxc-php5.dev lxc-landing.dev
     ```

     ![32](https://user-images.githubusercontent.com/93030868/138581869-1be90a7d-691b-4863-8ed9-2a32e613bb3f.png)

   * konfigurasi pada file domain
   
     ```markdown
     nano lxc_landing.dev
     ```

     ![image-20211024020625649](https://user-images.githubusercontent.com/93030868/138582127-d6b03e19-7cbe-453e-b681-9af39d40ea85.png)

   * masuk ke folder sites-enabled dan melakukan penyambungan atau *lean* yang berfungsi untuk membuat tautan antar file dan cek apakah syntax file yang dibuat sudah benar dan menjalankan ulang nginx
   
     ```markdown
     cd ../sites-enabled
     ln -s/etc/nginx/sites-availble/lxc_php5.dev
     nginx -t
     sudo nginx service start
     nginx -s reload
     ```

     ![34](https://user-images.githubusercontent.com/93030868/138581890-30655aa4-ea4a-467e-8c83-cfbbbd12e703.png)

   * konfigurasi host, sesuaikan dengan penamaan file domain dan ip yang dipakai
   
     ```markdown
     nano /etc/host
     ```

     ![36](https://user-images.githubusercontent.com/93030868/138582255-69633059-6c6e-448c-8942-016691de4e64.png)

   * masuk ke folder var/www/html lalu membuat sebuah folder yang nantinya akan diakses di browser. membuat file yang mengcopy dari file *index.nginx-debian.html* menjadi file baru *index.html*
   
     ```markdown
     cd /var/www/html
     mkdir landing
     cp index.nginx-debiam.html landing/index.html
     nano index.html
     ```

     ![36](https://user-images.githubusercontent.com/93030868/138582247-64bcf52b-edc5-4485-9cdc-39619135496a.png)

   * edit isi dalam file html sesuai apa yang ingin ditampilkan pada web

     ![image-20211024021222088](https://user-images.githubusercontent.com/93030868/138582293-7c909b27-4331-400f-9969-3627aaaa0578.png)

   * Jalan kan curl untuk melihat data yang akan muncul pada web yang mesih menggunakan bahas html
   
     ```markdown
     curl -i http://lxc_landing.dev
     ```

     ![image-20211024021300889](https://user-images.githubusercontent.com/93030868/138582307-e27c4d26-c57d-4629-aac4-cf1af8ba50d4.png)

   5. Lxc ubuntu_landing harus auto start ketika vm dinyalakan, hal ini digunakan untuk menjaga agar website comapny profile tidak mengalami *downtime*

      mengatur konfigurasi auto start kontainer lxc dengan masuk sebagai user root
   
      ```markdown
      sudo su
      cd /var/lib/lxc/ubuntu_landing
      ```

      tambahkan konfigurasi pada kontainer server utama
   
      ```markdown
      lxc.start.auto = 1
      ```

      ![image-20211024021622847](https://user-images.githubusercontent.com/93030868/138582317-9115047e-67c6-4657-a725-a20afdb097f0.png)

   6. setup nginx pada vm.local untuk mengatur *proxy_pass* dimana:

   * mengatur hosts dan menambah nama file domain serta ip yang digunakan pada masing-masing kontainer
   
   ```markdown
   sudo nano /etc/hosts
   ```

   ![image-20211024021814788](https://user-images.githubusercontent.com/93030868/138582329-19e9ff80-0844-423b-80fe-501d2b38e9b1.png)

   * Buat file vm.local di folder sites-available. Jika sudah memilik tidak perlu pakai tinggal lakukan konfigurasi
   
     ```markdown
     sudo /etc/nginx/sites-available/
     sudo touch vm.local
     ```

     ![image-20211024022052112](https://user-images.githubusercontent.com/93030868/138582339-61505fbb-82ea-4f4f-b9a8-948adc449999.png)
   
     * mengakses http://vm.local akan diarahkan ke http://lxc_landing.dev
     * mengakses http://vm.local/blog akan diarahkan ke http://lxc_php7.dev
     * mengakses http://vm.local/app akan diarahkan ke http://lxc_php5.dev

   * konfigurasi vm.lokal dan tambahkan masing-masing nama file dan tempat folder domain
   
     ```markdown
     sudo nano vm.local
     ```

     ![image-20211024022724560](https://user-images.githubusercontent.com/93030868/138582346-d75fe726-92a9-4b0a-8c17-47fed98a0b6f.png)
   * memperbarui konfigurasi
   
     ```markdown
     cd ../sites-enabled
     nginx -t
     nginx -s reload
     ```

     ![43](https://user-images.githubusercontent.com/93030868/138582008-6b6425e7-3960-4e74-bdc5-f1f2771b0f65.png)

   * menampilkan site utama atau site vm.local
   
     ```markdown
     curl -i http://vm.local
     ```

     ![image-20211024022914019](https://user-images.githubusercontent.com/93030868/138582357-56ea3671-bd5d-4e75-b2fc-d6fec91cce95.png)
   7. untuk kebutuhan presentasi mereka, browser di laptop mereka harus dapat mengakses ketiga url tersebut

   * web utama

     ![46](https://user-images.githubusercontent.com/93030868/138582023-62818cce-c5ba-463c-b18c-9b79c7087e7e.png)
   
   * Membuka di dalam brwoser windows menggunakan url vm.local
      Web Blog

     ![47](https://user-images.githubusercontent.com/93030868/138582025-336abacb-144b-4638-bf11-4cbed7022e54.png)

   * web aplikasi

     ![48](https://user-images.githubusercontent.com/93030868/138582026-15ed495a-90f6-49b7-a160-df36ace78c45.png)

   ------

   #### Analisa

     - mengapa untuk kebutuhan php5.6 tidak bisa menggunakan ubuntu 16.04, sehingga perlu diganti os ke debian 9?

        karena ubuntu 16.04 hanya tersedia untuk opsi yang berbayar yang hanya dapat diakses ubuntu estended security maintenance yang berarti ubuntu 16.04 akan dihapus karena adanya pembaruan fitur yang bernama Trusty EOL yang tidak support pada ubuntu 16.04

      - kenapa harus menggunakan virtualisasi LXC pada skema website yang akan didevelop?

        Karena LXC menggunakan file system yang netral, container dapat di fungsikan secara penuh oleh OS, data dapat disimpan di dalam maupun diluar container.

      - apa yang dimaksud dengan proxy server? kenapa vm.local bisa kita anggap sebagai proxy server?
   
        Proxy server bisa disebut computer sentral atau computer server yang bisa bertindak sebagai computer lainnya untuk yang dapat melakukan request content pada internet maupun intranet sama seperti vm.local. jadi vm.local bisa dikatakan juga sebagai proxy server
        
   #### Rerensi
   https://idcloudhost.com/
   
   https://www.dicoding.com/
 

