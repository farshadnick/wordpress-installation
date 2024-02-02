
## Install Mysql & PHP & PHP-FPM
```
apt update && apt install nginx mysql-server  php8.1 php-imagick php8.1-fpm php8.1-mbstring php8.1-bcmath php8.1-xml php8.1-mysql  php8.1-common php8.1-gd  php8.1-cli php8.1-curl php8.1-zip -y

```

##  Mysql Config for Wordpress 
```
CREATE DATABASE wordpress_db;
CREATE USER 'packops'@'localhost' IDENTIFIED BY '1234';
GRANT ALL PRIVILEGES ON wordpress_db.* to 'packops'@'localhost';
FLUSH PRIVILEGES;
```
## Download Wordpress
```
wget -O /tmp/wordpress.tar.gz https://wordpress.org/latest.tar.gz
tar -xzvf /tmp/wordpress.tar.gz -C /var/www
chown -R www-data.www-data /var/www/wordpress
```

## Set Your Database Configuration in wp-config.php
mv wp-config-sample.php wp-config.php

```
DB NAME ==> wordpress_db
DB USERNAME  packops
DB PASSWORD   1234
```

## Make your Nginx Config in  /etc/nginx/site-enabled/packops.local.conf

```
server {
        listen 80;
        listen [::]:80;
        root /var/www/wordpress/;
        index  index.php index.html index.htm;
        server_name packops.local www.packops.local;

        error_log /var/log/nginx/mysite.com_error.log;
        access_log /var/log/nginx/mysite.com_access.log;

        client_max_body_size 100M;
        location / {
                try_files $uri $uri/ /index.php?$args;
        }
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.4-fpm.sock;
                fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
}


```
