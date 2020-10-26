# install-php7-3-fpm-laravel

```
apt update && sudo apt install nginx
service nginx status
```

## Allow Port
```
ufw allow OpenSSH
ufw allow 'Nginx HTTP'
```

## Get IP Address
```
sudo ifconfig | grep -Eo 'inet (addr:)?([0-9]*\.){3}[0-9]*' | grep -Eo '([0-9]*\.){3}[0-9]*' | grep -v '127.0.0.1'
```

## Install PHP 7.3 FPM
```
apt-get install software-properties-common
add-apt-repository ppa:ondrej/php
apt-get update

apt-get install php7.3-fpm php7.3-cli php7.3-mysql php7.3-gd php7.3-imagick php7.3-recode php7.3-tidy php7.3-xmlrpc php7.3-common php7.3-curl php7.3-mbstring php7.3-xml php7.3-bcmath php7.3-bz2 php7.3-intl php7.3-json php7.3-readline php7.3-zip

php -v
```

## Grant Permission
```
chmod -R 755 /var/www
```

## Install Composer
```
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

## Install Laravel
```
composer create-project --prefer-dist laravel/laravel project-name
chown -R www-data:www-data /var/www/html/lab-axfon/
chmod -R 755 /var/www/html/lab-axfon/
```

## nginx Configuration
```
/etc/nginx/sites-available/default
```

```
server {
    listen 80;
    listen [::]:80;
    root /var/www/html/lab-axfon/public;
    index index.php index.html index.htm;
    server_name lab.axfon.com;

    location / {
         try_files $uri $uri/ /index.php?$args;        
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include snippets/fastcgi-php.conf;
        #
        #       # With php-fpm (or other unix sockets):
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        #       # With php-cgi (or other tcp sockets):
        #       fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

}
```
```
service nginx restart
```

## Update /etc/php/7.3/fpm/php-fpm.conf
```
emergency_restart_threshold 10
emergency_restart_interval 1m
process_control_timeout 10s
```

## Update /etc/php/7.3/fpm/pool.d/www.conf
```
pm = dynamic
pm.max_children = 5
pm.start_servers = 3
pm.min_spare_servers = 2
pm.max_spare_servers = 4
pm.max_requests = 200
```

## nginx.conf (/etc/nginx/nginx.conf) in http scope
```
fastcgi_buffers 8 16k;
fastcgi_buffer_size 32k;
fastcgi_connect_timeout 300;
fastcgi_send_timeout 300;
fastcgi_read_timeout 300;
```

## Set php.ini (/etc/php/7.3/fpm/php.ini)
```
max_execution_time = 300
```

## Set request_terminate_timeout (/etc/php/fpm/pool.d/www.conf)
```
request_terminate_timeout = 300
```
