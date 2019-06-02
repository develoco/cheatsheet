php, nginx, mariadb, mysql, apache, httpd, macos, mojave, homebrew, brew, fpm, fastcgi, cgi

# Install full stack on macOS

Get the formulas

```
brew install nginx mariadb php
```

Start/stop nginx

```
nginx
nginx -s reload
nginx -s stop
```

Start/stop mariadb

```
mysql.server start
mysql -uroot
mysql.server stop
```


Start PHP FPM

```
/usr/local/opt/php/sbin/php-fpm -F
```

`ctrl-c` to stop


Or start/stop everything as services

```
brew services start nginx
brew services start mariadb
brew services start php
```

## Relevant configurations files

    /usr/local/etc/nginx/nginx.conf
    
### Unmodified

    /usr/local/etc/php/7.3/php-fpm.conf
    /usr/local/etc/php/7.3/php-fpm.d/www.conf
    /usr/local/etc/nginx/servers/*

    /usr/local/etc/nginx/fastcgi.conf
    /usr/local/etc/nginx/fastcgi_params
    /usr/local/etc/php/7.3/php.ini

## Alternative Apache HTTPd

To enable PHP in Apache add the following to httpd.conf and restart Apache:
    LoadModule php7_module /usr/local/opt/php/lib/httpd/modules/libphp7.so

    <FilesMatch \.php$>
        SetHandler application/x-httpd-php
    </FilesMatch>

Finally, check DirectoryIndex includes index.php
    DirectoryIndex index.php index.html

The php.ini and php-fpm.ini file can be found in:
    /usr/local/etc/php/7.3/

## Alternative use macOS default PHP installation

Create a config

```
sudo cp /private/etc/php-fpm.conf.default /private/etc/php-fpm.conf
sudo cp /private/etc/php-fpm.d/www.conf.default /private/etc/php-fpm.d/www.conf
```

Launch the FastCGI

```
php-fpm -y /etc/php-fpm.conf -p /usr/local/var -F
```

# Config PHP and nginx


In ` /usr/local/etc/nginx/nginx.conf` add a ref to `index.php`

```
location / {
    root   html;
    index  index.html index.htm index.php;
}
```

Further down in the file uncomment some lines and `include fastcgi.conf`

```
location ~ \.php$ {
    root           html;
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    #fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    include        fastcgi.conf;
    include        fastcgi_params;
}
```

Reload configuration with `nginx -s reload`

Create a test file `/usr/local/var/www/info.php` with contents:

```
<?php
phpinfo();
```

Check `http://127.0.0.1:8080/info.php`  and verify that it works
