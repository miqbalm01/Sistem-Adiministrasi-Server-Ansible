# DNS

#### Grahito Ardani B 1202199006 || M Iqbal Maulana 1202190023

------

Pertama, untuk membuat sebuah subdomain dev.vm.local. Edit vm.local tambahkan subdomain

```markdown
sudo nano vm.local
```

![1](https://user-images.githubusercontent.com/93030868/146304687-15f09bb2-8265-42a9-a2df-8e8eb85e53ff.png)

Masuk kedalam Hosts tambahkan subdomain dev.vm.local, setelah itu jangan lupa lakukan restart nginx

```markdown
Sudo nano /etc/hosts
Sudo service nginx restart
```

![2](https://user-images.githubusercontent.com/93030868/146304692-acb15431-4294-498f-8d58-9007987cb24c.png)

Tambahkan Konfigurasi CNAME untuk memasukkan subdomain, CNAME digunakan untuk alias dari satu nama ke nama lain: pencarian DNS akan dilanjutkan dengan mencoba lagi pencarian dengan nama baru 

![3](https://user-images.githubusercontent.com/93030868/146304694-76d637bc-a8f2-4323-9660-f6278d78cddd.png)

```markdown
sudo nano /etc/bind/vm/vm.local
```

Restart bind9 dan coba lakukan ping ke subdomain kita buat

```markdown
Sudo service bind9 restart
Ping dev.vm.local

```

![4](https://user-images.githubusercontent.com/93030868/146304695-0ac861ce-aa3a-41ea-9f43-ab2edeed067c.png)

Disini kita lakukan curl untuk mengetahui isi dari subdomain itu

```markdown
Curl -i http://dev.vm.local 
```

![5](https://user-images.githubusercontent.com/93030868/146304699-d7350a80-76b5-4f12-839c-f3c766470877.png)

lalu kita masuk ke

```markdown
cd main.yml
```

lalu kita membuat direktori baru dan copy ke dalam direktori baru

![7](https://user-images.githubusercontent.com/93030868/146304702-96b236ec-0d4a-4335-aa42-c243e026950a.png)

![8](https://user-images.githubusercontent.com/93030868/146304704-5cb1fa1e-7ae0-48b7-95ff-d7de31445d3d.png)

lalu kita masuk ke

```markdown
cd roles/lv/templates
nano 43.168.192.in-addr.arpa
nano named.conf.local
nano named.conf.options
nano  resolv.conf
nano vm.local
```

![10](https://user-images.githubusercontent.com/93030868/146304710-74c1dd9e-013e-46d1-941e-4c104279df58.png)

![11](https://user-images.githubusercontent.com/93030868/146304713-200245c7-8ffe-4e8c-b2ae-4916f3f5f950.png)

![12](https://user-images.githubusercontent.com/93030868/146304717-af15836f-dd3c-41ca-86eb-6f86b0b41474.png)

![13](https://user-images.githubusercontent.com/93030868/146304718-55d4427c-9b7b-47bb-be17-56a3f352603b.png)

![14](https://user-images.githubusercontent.com/93030868/146304722-5624c7b5-cc08-49bf-9f4f-b23baab655e8.png)

lalu jalankan ansible playbook

```markdown
ansible-playbook -i hosts install-laravel.yml -k
```

![15](https://user-images.githubusercontent.com/93030868/146304723-e191a2b3-f41f-409f-8d7f-a4d13088b00f.png)

lalu lanjut keluar dari direktori dan masuk ke hosts dan tambahkan domain baru

```markdown
sudo nano /etc/hosts
```

![16](https://user-images.githubusercontent.com/93030868/146304725-b53805cc-015e-4e60-88c7-0efa33492f74.png)

lalu masuk ke dalam lxc_landing dan tambahkan subdomain

```markdown
sudo lxc-attach -n ubuntu_php7.4 
sudo nano /etc/hosts
```

![17](https://user-images.githubusercontent.com/93030868/146304729-dc5f16ac-e5cc-4760-995b-603a9ee0848b.png)

tambahkan juga subdomain kita ke dalam name server lxc_landing.dev lalu restart nginx

```markdown
cd /etc/nginx/sites-available
sudo service nginx restart
sudo nginx -t
sudo nginx -s reload
```

![18](https://user-images.githubusercontent.com/93030868/146304732-fb50b6cb-08e0-43b6-8b0f-77d07b1958ca.png)

lakukan Cname dan ping pada subdomain didalam lxc_landing

![19](https://user-images.githubusercontent.com/93030868/146304736-dc2ad528-dcaf-4dc5-8d6e-107f46a0289a.png)

lalu kita akses laravel

![22](https://user-images.githubusercontent.com/93030868/146304737-7a480377-95e4-4283-ae5e-b400d06a0e05.png)

akses Cl

![23](https://user-images.githubusercontent.com/93030868/146304980-1e8b096f-4b54-4c20-bd01-5f7dbada9853.png)

akses wordpress

![24](https://user-images.githubusercontent.com/93030868/146304973-e76544d9-a2fa-4295-8a6a-082f7b9db8de.png)

akses phpMyAdmin

![25](https://user-images.githubusercontent.com/93030868/146304977-90c3d6c0-5b6e-4f36-ae96-09ccb804b7b0.png)
