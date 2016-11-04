FROM smebberson/alpine-base:3.0.0

MAINTAINER Engin Terzi <https://github.com/enginterzi>

# Updating packages repo
RUN \
    echo '@testing http://nl.alpinelinux.org/alpine/edge/testing' >> /etc/apk/repositories && \
    echo '@community http://nl.alpinelinux.org/alpine/edge/community' >> /etc/apk/repositories


# Install php extensions
RUN \
    apk add --update --no-cache \
    wget \
    curl \
    py-pip \
    nginx \
    php5 \
    php5-bcmath \
    php5-cli \
    php5-fpm \
    php5-gd \
    php5-intl \ 
    php5-mcrypt \
    php5-pdo \
    php5-soap \
    php5-xml \
    php5-zip \
    php5-opcache \ 
    php5-redis@testing \
    php5-xdebug@community \
    php5-imagick 


RUN chown -R nginx:www-data /var/lib/nginx


 # Install dev APK packages to build php extension  
RUN \
    apk add \
    php5-dev \
    pcre-dev \
    build-base \
    autoconf \
    libtool
    
    # https://pecl.php.net/package/oauth
RUN \
    cd /tmp && \
    wget --no-check-certificate https://pecl.php.net/get/oauth-1.2.3.tgz && \
    tar xzf oauth-1.2.3.tgz && \
    cd oauth-1.2.3 && \
    phpize && ./configure --with-php-config=/usr/bin/php-config && make && make install && \
    echo 'extension=oauth.so' > /etc/php5/conf.d/oauth.ini

    # https://github.com/igbinary/igbinary/
    # https://pecl.php.net/package/igbinary
    # Igbinary is a drop in replacement for the standard php serializer. Instead of
    # time and space consuming textual representation, igbinary stores php data
    # structures in a compact binary form. Savings are significant when using
    # memcached or similar memory based storages for serialized data.
RUN \
    cd /tmp && \
    wget --no-check-certificate https://pecl.php.net/get/igbinary-1.2.1.tgz && \
    tar xzf /tmp/igbinary-1.2.1.tgz && \
    cd igbinary-1.2.1 && \
    phpize && ./configure --with-php-config=/usr/bin/php-config CFLAGS="-O2 -g" --enable-igbinary && make && make install && \
    echo 'extension=igbinary.so' > /etc/php5/conf.d/igbinary.ini
    
        


# Web servers configurations
# -- nginx conf => /etc/nginx/nginx.conf , /etc/nginx/conf.d/default.conf
# -- php-fpm conf => /etc/php5/fpm.d/www.conf , /etc/php5/php-fpm.conf
# -- xdebug conf  => /etc/php5/conf.d/xdebug.ini
COPY root /


# Add magecron for Magento
# Checkout https://pypi.python.org/pypi/schedule
RUN chmod -v +x /usr/local/bin/magecron

# Install Python schedule library for magecron to run
RUN curl https://bootstrap.pypa.io/get-pip.py | python && pip install schedule

# Purge dev APK packages
RUN \
    apk del \
        *-dev \
        build-base \
        autoconf \
        libtool \
        wget \
        curl 

RUN \    
    # Cleanup after phpizing
    rm -rf /usr/include/php /usr/lib/php/build /usr/lib/php5/modules/*.a

# Final cleanup
RUN \
    rm -rf /var/cache/apk/* /tmp/* /usr/share/man

EXPOSE 80 443

VOLUME ["/var/www/html", "/var/log/nginx", "/var/log/php-fpm"]



