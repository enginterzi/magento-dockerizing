# Magento seamless Dockerizing 
I have been working on clouds at the IaaS layer over 10 years. With Amazon AWS and Google GCE 
now providing large-scale cloud service for several years, it is fair to say that getting access
to a server has never been easy and quick.

However, application packing, configuration and composing of service in ditributed enviroment 
has not progressed much despite a lot of work in configuration management and orchestiration.

Deploying and running a distributed application at scale and in a fault-tolerant manner is still hard.

Dockerizing is not about full virtualization versus containers; it's about the easy of packing and running
an application.

As a developer, I spent a lot of time  to creating my development environment, I hope this Docker scratch 
will help everyone to up and run the Magento container for development and test purposes.

A Docker file installs required the Magento system requirements, PHP extensions, Apache and Nginx as well as PHP-FPM. 
The Nginx is default options but you can switch to the Apache with environment variable 


# Installation
The easy way to get up and running with this Docker container is to pull the latest stable versions from the [Docker Hub Registry](https://hub.docker.com/r/ente/magento/). 
I organized container tags as an OS and PHP version. Current OS and PHP versions are 
* Centos 7 and PHP55
* Centos 7 and PHP56

```
docker pull docker pull ente/magento:centos7-php56
```

# Usage
Environment variables;
* `$WEB_SERVER`   = used switching the web server. Default is Nginx. For Apache: `httpd` or `apache`
* `$XDEBUG_RHOST` = activates the xdebug and setts the PROXY ip for debuging
* `$UID`          = Modify apache UID
* `$GID`          = Modify apache GID

Used Volums;
* `/var/www/html`    = Magento application source codes
* `/var/log/httpd`   = Apache logs if it activated
* `/var/log/nginx`   = Nginx logs 
* `/var/log/php-fpm` = PHP-FPM logs

```
 sudo docker run  -p 80:80 --name docker-magento-nginx -d ente/magento:centos7-php56
```

if you'd like to switch Nginx to appache

```
sudo docker run  -p 80:80 --name docker-magento-httpd -e WEB_SERVER=httpd -d ente/magento:centos7-php56
```