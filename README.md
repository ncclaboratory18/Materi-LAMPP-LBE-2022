# LAMPP

## Apa itu LAMPP?

![alt text](./src/lampu.jpg)

Lampp merupakan singkatan dari Linux, Apache, MySQL, perl/php/python. Merupakan sebuah paket perangkat lunak bebas yang digunakan untuk menjalankan sebuah aplikasi secaralengkap

### Komponen-komponen dari LAMP:
- **Linux** Linux sendiri berperan sebagai sistem informasi yang berbasi Unix yang digunakan oleh pengguna secara gratis. Linux sendiri merupakan perwakilan dari sistem lainnya seperti FreeBSD, NetBSD, OpenBSD dan Darwin/Mac OS X.
- **Apache** Komponen ke dua ini adalah web browser open source yang artinya dapat digunakan secara gratis. Fungsi utama Apache ini dapat menghasilkan halam web yang telah programmer kerjakan  menggunakan bahasa pemrogramman PHP.
- **MySQL** Terdengan cukup familiar memang, komponen ini merupakan sistem database yang sering digunakan bersama PHP.
- **Perl atau PHP atau Pyton**  PHP adalah sebuah bahasa pemrogramman untuk memnbuat suatu web. Selain Windows, PHP juga dapat digunakan pada sistem operasi Linux dan lainnya.

Beberapa perangkat lunak yang menggunakan konfigurasi LAMP antara lain MediaWiki dan Bugzilla.

### Kelebihan menggunakan LAMPP
- memiliki sistem database yang keamanannya terjamin
- proses development yang lebih cepat
- dapat dikustomisasi sesuai kebutuhan developer
- kode di dalamnya dapat bekerja pada berbagai OS, seperti Windows, Linux, Android, dan iOS

## Install LAMPP
Pertama-tama, update dahulu dependency version.
```
sudo apt update
```

### Apache2
Install apache2 dengan command berikut:
```
sudo apt install apache2
```
Lalu, kita perlu mengatur konfigurasi firewall (UFW) untuk mengizinkan HTTP protocol yang diinginkan.
```
sudo ufw app list
```
![image](https://user-images.githubusercontent.com/78243059/195121362-20b9f73c-f1a2-4271-9442-844901a5c180.png)  
Deskripsi:
1. Apache: terbuka untuk port 80 (HTTP)
2. Apache Full: terbuka untuk port 80 (HTTP) dan 443 (HTTPS)
3. Apache Secure: terbuka untuk port 443 (HTTPS)

Kali ini, kita akan membuka koneksi baik untuk protokol HTTP dan HTTPS dengan command berikut
```
sudo ufw allow in "Apache Full"
```
Cek perubahannya dengan command ini
```
sudo ufw status
```

Pastikan web server sudah menyala dengan command berikut
```
sudo service apache2 start
```
Web server bisa diakses pada *http://localhost* atau menggunakan server public IP address yang bisa dicek dengan cara
```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```
lalu dapat diakses dengan *http://\<ip-address-server\>*

### MySQL
Install mysql dengan command berikut
```
sudo apt install mysql-server
```
Setelah mysql terinstall, kita perlu menjalankan servicenya dahulu dengan command
```
sudo service mysql start
```
Maka, mysql bisa langsung diakses dengan
```
sudo mysql
```
Walaupun opsional, namun direkomendasikan untuk mengonfigurasi keamanan database. MySQL dilengkapi dengan script yang bisa menghapus pengaturan bawaan yang kurang aman dan mengunci akses ke sistem database.
```
sudo mysql_secure_installation
```
![image](https://user-images.githubusercontent.com/78243059/195124659-e1d775c0-93cc-4c57-914c-9c9c43a094a3.png)

![image](https://user-images.githubusercontent.com/78243059/195124766-8381395b-4e41-4794-81e1-e5b3b4b19b6b.png)

![image](https://user-images.githubusercontent.com/78243059/195124979-d6778e09-4b99-4964-afa6-56d0d67d2281.png)

Tekan Y atau Enter untuk sisa pertanyaan yang lain untuk menghapus user anonymous, mengahpus database testing, menutup akses remote, dan menerapkan peraturan-peraturan baru yang baru saja dikonfigurasi.

Jika menemukan error *Failed! Error: SET PASSWORD has no significance for user 'root'@'localhost' as the authentication method used doesn't store authentication data in the MySQL server. Please consider using ALTER USER instead if you want to change authentication parameters.*, maka alter password dahulu pada database.
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'mynewpassword'
```

Database bisa diakses dengan command
```
sudo mysql -u root -p
```
Masukkan password yang tadi sudah diatur, lalu database sudah berhasil diakses.

### PHP
Install php dengan command
```
sudo apt install php libapache2-mod-php php-mysql
```
Kita juga menginstall library libapache2-mod-php (untuk memungkinkan Apache menangani file php) dan php-mysql (memungkinkan php berinteraksi dengan mysql).

Kita bisa mengecek apakah php sudah berhasil terinstall dengan command
```
php -v
```
![image](https://user-images.githubusercontent.com/78243059/195126664-432e4856-cd4c-4755-854a-77f3a50158e8.png)


# Tambahan

## Mengubah Root File
Saat mengakses *http://localhost*, server secara default akan membuka file *index.html* yang berada di */var/www/html*. Pengaturan ini diatur di */etc/apache2/mods-enabled/dir.conf* dan bisa kita ubah.
![image](https://user-images.githubusercontent.com/78243059/195129652-c10f47b8-c895-4641-8ec3-572045c6cc77.png)

File di sebelah kiri memliki prioritas untuk dibuka pertama lebih dahulu. Kita bisa mengubah urutan file tersebut, menghapus, maupun menambahkan file yang baru.   Contoh: kita bisa menambahkan file *info.php* di paling kiri untuk dibuka secara default.
```
DirectoryIndex info.php index.html index.cgi index.pl index.php index.xhtml index.htm
```
Lalu di */var/www/html*, kita perlu membuat file *info.php* tersebut dengan command
```
sudo nano /var/www/html/info.php 
```
Lalu isi file dengan syntax
```
<?php
phpinfo();
```
Simpan file, lalu restart server dengan command
```
sudo service apache2 restart
```
Buka kembali halaman *http://localhost* untuk melihat perubahannya.

### Mengubah Root Directory
Root directory diatur di */etc/apache2/sites-available/000-default.conf*, tepatnya pada line ini
```
DocumentRoot /var/www/html
```
Kita bisa mengubahnya sesuai path yang kita mau. Misalnya, kita menaikkan path satu langkah menjadi
```
DocumentRoot /var/www
```
Restart server. Maka, server akan mencari file info.php dan lain-lainnya tadi pada path */var/www*. Server tidak akan menyajikan file tersebut jika tidak berhasil menemukannya.  
Untuk itu, kita perlu memindahkan file info.php ke root path tersebut
```
sudo mv /var/www/html/info.php /var/www/info.php 
```
Akses kembali *http://localhost* dan halaman info.php berhasil muncul.

## Konfigurasi Default Port
Saat kita mengakses suatu URL, secara default kita mengakses port 80  (protocol HTTP), begitupun juga Apache yang secara default menyajikan halaman pada port 80. Kita bisa mengubah pengaturan ini di /etc/apache2/ports.conf
```
Listen 80

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
```
Kita cukup mengubah Listen 80 menjadi port lain yang kita inginkan. Restart server, lalu akses *http://localhost:\<port-number\>*.

## Konfigurasi Virtual Host
Selain mengakses web server dengan nama localhost atau IP server, kita bisa meng-custom nama domain kita sendiri.  
Kita buat folder yang akan menyimpan file-file web kita dengan
```
sudo mkdir -p /var/www/your_domain_1/public_html
```
Atur permission pada folder sehingga bisa kita edit.
```bash:
sudo chmod -R 755 /var/www/your_domain
```
Buat halaman web kalian pada folder tersebut.
```
sudo nano /var/www/your_domain/index.html
```
isi file html untuk menandakan page tersebut
```html:
<html>
  <head>
    <title>Welcome to your_domain_1!</title>
  </head>
  <body>
    <h1>Success! The your_domain_1 virtual host is working!</h1>
  </body>
</html>
```
membuat Virtual Host Files
```script:
sudo nano /etc/apache2/sites-available/your_domain_1.conf
```
isi file `your_domain_1.conf` dengan isi sebagai berikut :
```conf:
<VirtualHost *:80>
   	ServerAdmin admin@your_domain_1
    	ServerName your_domain_1
        ServerAlias your_domain_1
        DocumentRoot /var/www/your_domain_1/public_html

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enabling the New Virtual Host Files
```bash:
sudo a2ensite your_domain_1.conf
```
disable the default site
```bash:
sudo a2dissite 000-default.conf
```
restart apache2 untuk melihat keajaiban
```bash:
sudo service apache2 restart
```
virtual host dapat di akses pada [your_domain_1](http://your_domain_1)
