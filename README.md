apt update && apt install mysql-server install php7.4 php-imagick php7.4-fpm php7.4-mbstring php7.4-bcmath php7.4-xml php7.4-mysql  php7.4-common php7.4-gd php7.4-json php7.4-cli php7.4-curl php7.4-zip -y


#  Mysql Config for Wordpress 
```
CREATE DATABASE wordpress_db;
CREATE USER 'packops'@'localhost' IDENTIFIED BY '1234';
GRANT ALL PRIVILEGES ON wordpress_db.* to 'packops'@'localhost';
FLUSH PRIVILEGES;
```
# Download Wordpress
```
wget -O /tmp/wordpress.tar.gz https://wordpress.org/latest.tar.gz
tar -xzvf /tmp/wordpress.tar.gz -C /var/www
chown -R www-data.www-data /var/www/wordpress
```

#Set Your Database Configuration in wp-config.php
mv wp-config-sample.php wp-config.php

```
DB NAME ==> wordpres_db
DB USERNAME  packops
DB PASSWORD   1234
```

# Make your Nginx Config in  /etc/nginx/site-enabled/packops.local.conf

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
