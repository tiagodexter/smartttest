# smartt Test

This repo is for smarttBot Sysadmin Test

I used FreeBSD OS because of the stability and security of the system.
For that, I also used ports to compile and install all required packages as below:

Installing the packages
```
cd /usr/ports/www/nginx
make install clean BATCH=YES

cd /usr/ports/databases/mariadb102-server
make install clean BATCH=YES

cd /usr/ports/lang/php71
make install clean BATCH=YES

cd /usr/ports/lang/php71-extensions
make install clean
```
Here I'd set the option ZLIB because it's one requirement for Wordpress

Setting initialization of the services

```
echo 'mysql_enable="YES"' >> /etc/rc.conf
echo 'php_fpm_enable="YES"' >> /etc/rc.conf
echo 'nginx_enable="YES"' >> /etc/rc.conf
```

Starting the system
```
service mysql-server start
service php_fpm start
```

Creating the SSL certificate
```
mkdir /usr/local/etc/nginx/ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /usr/local/etc/nginx/ssl/nginx.key -out /usr/local/etc/nginx/ssl/nginx.crt
```

Configure nginx.conf

Change, or put these lines on nginx.conf
```
listen 443 ssl;
server_name  localhost smartt.tk www.smartt.tk;
ssl_certificate /usr/local/etc/nginx/ssl/nginx.crt;
ssl_certificate_key /usr/local/etc/nginx/ssl/nginx.key;

location / {
            root   /usr/local/www/nginx;
            index  index.php index.html index.htm;
            try_files $uri $uri/ /index.php?q=$uri&$args;
        }

location ~ \.php$ {
                root           html;
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_param SCRIPT_FILENAME /usr/local/www/nginx$fastcgi_script_name;
                include        fastcgi_params;
        }
```

After this, just start nginx

```
service nginx start
```

Change password for MariaDB
```
mysql_secure_installation
```

Get Wordpress and extract it:
```
fetch https://wordpress.org/latest.zip
unzip latest.zip
mv wordpress/* .
rm -rf wordpress/
```
After this, just open your browser and install the wordpress.



