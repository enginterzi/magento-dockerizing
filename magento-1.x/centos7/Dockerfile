FROM centos:7

MAINTAINER Engin Terzi <me@enginterzi.com>

#Add the NGINX repository
# ref: http://nginx.org/en/linux_packages.html

COPY etc/nginx/nginx.repo /etc/yum.repos.d/nginx.repo

# Install EPEL and Remi
RUN yum -y install http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

ENV PHP_VERSION remi-php56

# Enable Remi PHP base on PHP_VERSION  env variable
RUN yum-config-manager --enable $PHP_VERSION


RUN yum -y install  nginx httpd supervisor which \
                    php \
                    php-bcmath \
                    php-cli \
                    php-fpm \
                    php-gd \
                    php-intl \
                    php-mbstring \
                    php-mcrypt \
                    php-mysqlnd \
                    php-pdo \
                    php-pecl-igbinary \
                    php-pecl-imagick \
                    php-pecl-oauth \
                    php-pecl-redis \
                    php-pecl-xdebug \
                    php-pecl-zendopcache \
                    php-process \
                    php-soap \
                    php-xml \
                    php-zip && yum clean all

# Web servers configurations
COPY etc/httpd/httpd.conf /etc/httpd/conf/httpd.conf
COPY etc/nginx/nginx.conf /etc/nginx/nginx.conf
COPY etc/nginx/default.conf /etc/nginx/conf.d/default.conf

# PHP-FPM configuration
COPY etc/php-fpm/www.conf /etc/php-fpm.d/www.conf

# Development xdebug configuration
COPY etc/xdbug/xdebug.ini /etc/php.d/xdebug.ini

# Copy Supervisor configuration
COPY etc/supervisor/magecron.ini /etc/supervisord.d/magecron.ini
COPY etc/supervisor/httpd.ini /etc/supervisord.d/httpd.ini.sample
COPY etc/supervisor/nginx.ini /etc/supervisord.d/nginx.ini.sample

# Add magecron for Magento
# Checkout https://pypi.python.org/pypi/schedule
COPY bin/magecron /usr/local/bin/magecron
RUN chmod -v +x /usr/local/bin/magecron

# Install Python schedule library for magecron to run
RUN curl https://bootstrap.pypa.io/get-pip.py | python && pip install schedule

# Add magerun script
COPY bin/magerun /usr/local/bin/magerun
RUN chmod -v +x /usr/local/bin/magerun

EXPOSE 80 443

VOLUME ["/var/www/html", "/var/log/httpd", "/var/log/nginx", "/var/log/php-fpm"]

CMD ["magerun"]

