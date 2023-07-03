---
title: "Setup OJS Ubuntu Server 20.04"
date: 2023-07-03T21:03:42+09:00
draft: false
---
## 1. Konfigurasi Web Server (Apache2)

````sh
apt install apache2 -y
````

## 2. Konfigurasi Virtual Host

````sh
nano /etc/apache2/sites-available/jurnal.charlesid.dev.conf
````

isikan kode berikut pada file tersebut : 

````sh
<VirtualHost *:80>
    ServerName jurnal.charlesid.dev
    DocumentRoot /var/www/jurnal.charlesid.dev
    <Directory /var/www/jurnal.charlesid.dev>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog /var/log/apache2/jurnal.charlesid.dev_error.log
    CustomLog /var/log/apache2/jurnal.charlesid.dev_access.log combined
</VirtualHost>
````

````sh
mkdir /var/www/jurnal.charlesid.dev
echo "open journal system" > /var/www/jurnal.charlesid.dev/index.html
````

````sh
a2ensite jurnal.charlesid.dev.conf
a2enmod rewrite
systemctl status apache2
````
Tes Browsing http://jurnal.charlesid.dev

## 3. Install SSL

````sh
snap install --classic certbot
certbot --apache -d jurnal.charlesid.dev
````

## 4. Install PHP
````sh
apt install php libapache2-mod-php php-cli php-common php-mbstring php-gd php-intl php-xml php-mysql php-zip php-json -y
````

````sh
systemctl restart apache2
systemctl status apache2
````

````sh
echo "<?php phpinfo(); ?>" > /var/www/jurnal.charlesid.dev/info.php
````
Tes browsing https://jurnal.charles.id/info.php

## 5. Install Database

````sh
apt install mariadb-server mariadb-client -y
mysql_secure_installation
````

````sh
Enter current password for root (enter for none): ENTER
Set root password? [Y/n] y
Remove anonymous users? [Y/n] y
Disallow root login remotely? [Y/n] y
Remove test database and access to it? [Y/n] y
Reload privilege tables now? [Y/n] y
````

````sh
sudo mysql
````

````sql
CREATE DATABASE ojs;
CREATE USER 'ojsuser'@'localhost' IDENTIFIED BY 'ojspass';
GRANT ALL PRIVILEGES ON ojs.* TO 'ojsuser'@'localhost';
FLUSH PRIVILEGES;	
EXIT;
````

````sh
wget -c http://pkp.sfu.ca/ojs/download/ojs-3.3.0-7.tar.gz
wget -c https://pkp.sfu.ca/ojs/download/ojs-3.2.1-4.tar.gz
````

````sh
rm -rf /var/www/jurnal.charles.id
tar xzvf ojs-3.3.0-7.tar.gz
mv ojs-3.3.0-7 /var/www/jurnal.charles.id
chown -R www-data:www-data /var/www/jurnal.charles.id
````

````sh
mkdir /var/www/ojs-files
chown -R www-data:www-data /var/www/ojs-files
````