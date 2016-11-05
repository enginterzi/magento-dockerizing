# Magento alpine-nginx-php-fpm
A Docker image for running nginx, php-fpm, based on Alphine Linux.
This image highly configurable Docker images running on Alphine and [s6][s6] process management.

## Features
This image features:

- Alphine Linux
- [s6][s6] and [s6-overlay][s6overlay]
- [Nginx][nginx]
- PHP-fpm
- mbstring 
- mcrypt 
- mysqlnd 
- php
- php-bcmath 
- php-cli  
- php-gd 
- php-intl  
- php-mcrypt 
- php-pdo 
- php-soap
- php-xml 
- php-zip 
- php-opcache  
- php-redis 
- php-xdebug 
- php-imagick 

## Usage

To use this image inlude `FROM ente/magento:alphine.3.3.0-php56` at the top of your `Dockerfile` or simply `dokcer run -p 80:80 -p 443:443 --name magento ente/magento:alphine.3.3.0-php56`.

Nginx and PHP-fpm logs (access and error logs) aren't automatically to `stdout` and `stder`. To review the logs, you can do one of two thins

Run the following in another terminal window:

```
docker exec -i magento tail -f /var/log/nginx/access.log -f /var/log/nginx/error.log -f /var/log/php-fpm/error.log
```

or 

Use Volums;
* `/var/log/nginx`   = Nginx logs 
* `/var/log/php-fpm` = PHP-FPM logs

## Customisation

This container comes setup as follows:

- s6 will automatically start nginx,php-fpm for you.
- If nginx or php-fpm dies, so will the container.
- nginx configuration  
- nginx magento site configuration
- PHP-fpm configuration
- xdebug settings
- Scheduling cron process
- PHP info php file.

### Magento Application content

To alter the application content that nginx serves up (add your website files), add the following in your Dockerfile:

```
ADD /path/to/content /var/www/html
```

or

Use Volume  
* `/var/www/html`    = Magento application source codes

```
`dokcer run -d -p 80:80 -p 443:443 -v /path/to/content:/var/www/html --name magento ente/magento:alphine.3.3.0-php56`
```

### Nginx configuration

Magento nginx configurations are supplied with this image. But it's easy to overwrite:

- Create your own `nginx.conf`.
- In your `Dockerfile`, make sure your `nginx.conf` file is copied to `/etc/nginx/nginx.conf`.
- Create your own `default.conf` for Magento Web site
- In your `Dockerfile`, make sure your `default.conf` file is copied to `/etc/nginx/conf.d/default.conf`.


**Make sure you start nginx without daemon mode, by including `daemon off;` in your nginx configuration, otherwise the container will constantly exit right after nginx starts.**

### Restarting services

This container runs following process 

- Nginx
- Php-fpm
- Magerun 
- Magecron

If you're running another process to keep track of something down-stream (for example, automatically updating nginx proxy settings when a down-stream application server (nodejs, php, etc) restarts) execute the command `s6-svc -h /var/run/s6/services/nginx` to send a `SIGHUP` to nginx and have it reload its configuration, launch new worker process(es) using this new configuration, while gracefully shutting down the old worker processes.

### nginx or php-fpm crash

By default, if nginx or php-fpm crashes, the container will stop. This has been configured within `root/etc/services.d/nginx/finish`. This is so the host machine can handle any issues, and automatically restart it (the docker way, `docker run --autorestart`).

If you don't want this to happen, simply replace the `root/etc/services.d/nginx/finish` with a different file in your image. I like to `ln -s /bin/true /root/etc/services.d/nginx/finish` in those instances in which you don't need a finish script and want to have the service restarted by s6.

### Nginx start up configuration

If you need to run a setup script before starting nginx. During your Dockerfile build process, copy across a file to `/etc/services.d/nginx/run` with the following (or customise it as required):

```
#!/usr/bin/with-contenv sh

if [ -e ./setup ]; then
./setup
fi

# Start nginx.
nginx -g "daemon off;"
```

# Magerun services and xdebug

Magerun execute some commends to change php.ini configuration and activate xdebug.

To activate the xdebug

```
`dokcer run -d -p 80:80 -p 443:443 -e XDEBUG_RHOST=PROXY ip for debuging --name magento ente/magento:alphine.3.3.0-php56`
```

# Scheduling cron

* Magecron schedules every 30 min. cron job for magento.
