Running Yii 2.0 on HHVM
=======================

HHVM is an alternative PHP engine made by Facebook which is performing significantly better than
current PHP 5.6 (and much better than PHP 5.5 or PHP 5.4). You can get 10–40% performance improvement
for virtually any application. For processing-intensive ones it could be times faster than with usual Zend PHP.

Linux only
-----------

HHVM is linux only. It doesn't have anything for Windows and for MacOS it works in limited mode without JIT compiler.


Installing HHVM
---------------

Installing is easy. Here's how to do it for Debian:

```
sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0x5a16e7281be7a449
echo deb http://dl.hhvm.com/debian wheezy main | sudo tee /etc/apt/sources.list.d/hhvm.list
sudo apt-get update
sudo apt-get install hhvm
```

Instructions for other distributions [are available](https://github.com/facebook/hhvm/wiki/Getting-Started).

Nginx config
------------

You can have both HHVM and php-fpm on the same server. Switching between them using nginx is easy since
both are working as fastcgi. You can even have these side by side. In order to do it you should run regular PHP
on one port and HHVM on another.

```
server {
    listen 80;
    root /path/to/your/www/root/goes/here;

    index index.php;
    server_name hhvm.test.local;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;
        fastcgi_pass   127.0.0.1:9000;
        try_files $uri =404;
    }
}

server {
    listen 80;
    root /path/to/your/www/root/goes/here;

    index index.php;
    server_name php.test.local;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;
        fastcgi_pass   127.0.0.1:9000;
        try_files $uri =404;
    }
}
```

As you can see, configurations are identical except port number.

Test it first
-----------

HHVM is more or less [tested to work with most frameworks out there](http://hhvm.com/frameworks/). Yii isn't exception.
Except minor issues everything should work. Still, there are
[many incompatibilities compared to PHP](https://github.com/facebook/hhvm/labels/php5%20incompatibility) so make sure to
test application well before going live.

Error reporting
---------------

HHVM behavior about errors is different than PHP one so by default you're getting nothing but a blank white screen instead
of an error. Add the following to HHVM config to fix it:

```
hhvm.debug.server_error_message = true
```


