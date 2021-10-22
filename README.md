# wordpress-lemp-stack
Dalam tutorial ini, Anda akan fokus untuk mendapatkan instance WordPress yang disiapkan dengan LEMP Stack (Linux, Nginx, MySQL, dan PHP)


## Install NGINX:
sudo apt install nginx

sudo /etc/init.d/nginx status (untuk melihat status nginx)

sudo /etc/init.d/nginx restart (untuk restart nginx)

![image](https://user-images.githubusercontent.com/71916387/138408203-251328e9-e7f9-47eb-9328-dbf8241ee4d7.png)


## konfigurasi wordpress.conf direktori nginx:
sudo nano `/etc/nginx/sites-available/wordpress.conf`

```
server {

	root /var/www/html/wordpress;
	index index.php index.html index.htm;
	server_name demo.pmii.id;


	access_log /var/log/nginx/wordpress_access.log;
	error_log /var/log/nginx/wordpress_error.log;

	client_max_body_size 64M;

	location / {
		try_files $uri $uri/ /index.php?$args;
		}

	location ~ \.php$ {
		try_files $uri =404;
		include /etc/nginx/fastcgi_params;
		fastcgi_read_timeout 3600s;
		fastcgi_buffer_size 128k;
		fastcgi_buffers 4 128k;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		fastcgi_pass unix:/run/php/php7.3-fpm.sock;
		fastcgi_index index.php;
			}

	
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/demo.pmii.id/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/demo.pmii.id/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = demo.pmii.id) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


        listen 80;
        server_name demo.pmii.id;
    return 404; # managed by Certbot


}

```
## Install MariaDB:
sudo apt -y install mariadb-server mariadb-client
sudo mysql_secure_installation
```
Enter current password for root (enter for none): Tekan Enter Saja
Set root password? [Y/n]: Y
New password: Masukkan Password
Re-enter new password: Ulangi Password
Remove anonymous users? [Y/n]: Y
Disallow root login remotely? [Y/n]: Y
Remove test database and access to it? [Y/n]: Y
Reload privilege tables now? [Y/n]: Y
```
sudo systemctl enable mariadb

sudo systemctl restart mariadb

sudo systemctl status mariadb

## Install PHP Paket:

sudo apt-get install php7.3-fpm

sudo apt install php7.3-common php7.3-mbstring php7.3-xmlrpc php7.3-soap php7.3-gd php7.3-xml php7.3-intl php7.3-mysql php7.3-cli php7.3-ldap php7.3-zip php7.3-curl

## Membuat user database untuk wordpress
```
sudo mysql -u root -p 

CREATE DATABASE wordpress;

CREATE USER 'wordpress'@'%' IDENTIFIED BY 'demowordpress';
  
GRANT ALL ON wordpress.* TO wordpress@'%';

FLUSH PRIVILEGES;
```
Allow akses dari server mariadb-client
sudo nano `/etc/mysql/mariadb.conf.d/50-server.cnf`

Tambahkan:
```
bind-address            = 0.0.0.0
```
## Download WordPress
  ```
  cd /tmp
  wget -c https://wordpress.org/latest.tar.gz -O wordpress.tar.gz
  ```
  
  Extract file `wordpress.tar.gz`
  ```
  tar xzvf wordpress.tar.gz
  ```
  
  pihakan folder `wordpress ke /var/www/html/`
  
  ```
  mv wordpress /var/www/html/wordpress
  ```
  setting permission secara rekursif pada folder `wordpress` agar bisa dibaca nginx
  ```
  chown -R www-data:www-data /var/www/wordpress
  ```
## proses installasi worpress pada web browser
Browsing `http://ip-server/wordpress`. Pastikan Ngix web server dan MariaDB sudah dalam status `Running`.

ikuti tahapannya sampai selesai!

**hasilnya akan seperti ini, sebagai contoh https://demo.pmii.id/:**
![image](https://user-images.githubusercontent.com/71916387/138415893-39cdc552-4291-4cbb-8f02-0f66e6dde010.png)


## Install SSL dengan certbot

```
sudo apt-get install certbot
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d demo.pmii.id
```

# SEKIAN
