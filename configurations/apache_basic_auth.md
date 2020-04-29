# konfigurasi password protected (basic authentication) subfolder di apache2

### __1. install package yang diperlukan__
```
# apt-get install apache2 apache2-utils
```

### __2. membuat subfolder di dalam document root apache__
```
# mkdir /var/www/html/redis			# disini saya membuat dir bernama redis

# touch /var/www/html/redis/index.php		# disini saya membuat file (optional)
```

### __3. membuat user dan password untuk authentikasi__

sintak: `htpasswd -c /etc/apache2/.htpasswd [nama user]` 
- "-c" digunakan untuk membuat passwdfile

```
# htpasswd -c /etc/apache2/.htpasswd skill39
```

 setelah itu akan muncul form untuk mengisi password

### __4. konfigurasi pengaturan direktory di 000-default.conf__

```
# nano /etc/apache2/sites-available/000-default.conf
```
tambahkan konfigurasi seperti ini pada virtual host 80 untuk http dan 443 untuk https (jika kalian menggunakan https)
```	
<Directory "/var/www/html/redis">
	AuthType Basic
	AuthName "Redis Database Login"			# valuenya bebas
	AuthUserFile /etc/apache2/.htpasswd
	Require valid-user
</Directory>
```

### __5. konfigurasi pada main file apache2, untuk mengkonfigurasi access control kepala file .htaccess__
```
# nano /etc/apache2/apache2.conf
```
tambahkan konfigurasi seperti ini pada bagian paling bawah
```
<Directory /var/www>
	Options Indexes FollowSymLinks
	AllowOverride All
	Require all granted
</Directory>
```

### __6. restart service apache2__
```
# service apache2 restart
```

### __7. testing__

masuk ke web broser, lalu ketikan
```
http://[ip atau domain server]/redis		# jika http
```
atau
```
https://[ip atau domain server]/redis		# jika https
```

nanti akan muncul pop up box dengan tulisan "Redis Database Login" sesuai dengan value yang diisi di AuthName

tinggal masukan user dan password yang telah dibuat tadi untuk dapat mengakses index.php didalam folder redis

---
### __LINK PENTING__

- [digitalocean blog](https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-apache-on-ubuntu-14-04)
