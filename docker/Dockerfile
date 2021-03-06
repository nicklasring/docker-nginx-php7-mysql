FROM ubuntu:16.04
MAINTAINER Nicklas Ring <niklatz@gmail.com>

# Keep upstart from complaining
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl
RUN mkdir /run/php

# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update

# Basic Requirements
RUN apt-get -y install pwgen python-setuptools curl git nano sudo unzip openssh-server openssl vim htop
RUN apt-get -y install mysql-client nginx php-fpm php-mysql

# PHP Requirements
RUN apt-get -y install php-xml php-mbstring php-bcmath php-zip php-pdo-mysql php-curl php-gd php-intl php-pear php-imagick php-imap php-mcrypt php-memcache php-apcu php-pspell php-recode php-tidy php-xmlrpc composer

# nginx config
RUN sed -i -e"s/user\s*www-data;/user dev www-data;/" /etc/nginx/nginx.conf
RUN sed -i -e"s/keepalive_timeout\s*65/keepalive_timeout 2/" /etc/nginx/nginx.conf
RUN sed -i -e"s/keepalive_timeout 2/keepalive_timeout 2;\n\tclient_max_body_size 100m/" /etc/nginx/nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# php-fpm config
RUN sed -i -e "s/upload_max_filesize\s*=\s*2M/upload_max_filesize = 100M/g" /etc/php/7.0/fpm/php.ini
RUN sed -i -e "s/post_max_size\s*=\s*8M/post_max_size = 100M/g" /etc/php/7.0/fpm/php.ini
RUN sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php/7.0/fpm/php-fpm.conf
RUN sed -i -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" /etc/php/7.0/fpm/pool.d/www.conf
RUN sed -i -e "s/user\s*=\s*www-data/user = dev/g" /etc/php/7.0/fpm/pool.d/www.conf

# nginx site conf
ADD ./nginx-site.conf /etc/nginx/sites-available/default

# Supervisor Config
RUN /usr/bin/easy_install supervisor
RUN /usr/bin/easy_install supervisor-stdout
ADD ./supervisord.conf /etc/supervisord.conf

# Add system user for dev
RUN useradd -m -d /home/dev -p $(openssl passwd -1 'dev') -G root -s /bin/bash dev \
    && usermod -a -G www-data dev \
    && usermod -a -G sudo dev \
    && ln -s /usr/share/nginx/www /home/dev/www

# Initialization and Startup Script
ADD ./start.sh /start.sh
RUN chmod 755 /start.sh

#NETWORK PORTS
EXPOSE 80

# Run
CMD ["/bin/bash", "/start.sh"]
