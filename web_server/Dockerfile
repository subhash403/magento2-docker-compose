FROM ubuntu:18.04
MAINTAINER subhash <subhash.b@easywaytech.com>

RUN apt-get update -y
RUN apt-get install software-properties-common -y
RUN add-apt-repository ppa:ondrej/php -y
ENV DEBIAN_FRONTEND=noninteractive
COPY libapache2-mod-fastcgi_2.4.7~0910052141-1.2_amd64.deb /tmp/libapache2-mod-fastcgi_2.4.7~0910052141-1.2_amd64.deb
RUN cd /tmp
RUN dpkg -i libapache2-mod-fastcgi_2.4.7~0910052141-1.2_amd64.deb; apt install -f


# Install apache, PHP, and supplimentary programs. openssh-server, curl, and lynx-cur are for debugging the container.
RUN apt-get update && apt-get -y upgrade && DEBIAN_FRONTEND=noninteractive 
RUN apt-get -y install \
    apache2 php7.0 php7.0-mysql php7.0 php7.0-curl php7.0-mysql libapache2-mod-php7.0 \
php7.0-bcmath php7.0-curl php7.0-gd php7.0-intl php7.0-mbstring php7.0-mcrypt \
php7.0-soap php7.0-xml php7.0-xsl php7.0-zip php7.0-json php7.0-iconv libapache2-mod-php7.0 curl -y

# Enable apache mods.
RUN a2enmod php7.0
RUN a2enmod rewrite

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php/7.0/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php/7.0/apache2/php.ini

# Manually set up the apache environment variables
ENV APACHE_RUN_USER root
ENV APACHE_RUN_GROUP root
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

# Expose apache.
EXPOSE 80

# Copy this repo into place.
ADD www /var/www/site

# Update the default apache site with the config we created.
ADD apache-config.conf /etc/apache2/sites-enabled/000-default.conf

# By default start up apache in the foreground, override with /bin/bash for interative.
CMD /usr/sbin/apache2ctl -D FOREGROUND
RUN mkdir /magento
RUN cd /magento 
COPY magento2-2.2.5/ /var/www/html/magento
CMD chown -R www-data:www-data /var/www/html/ && chmod -R 755 /var/www/html/
RUN chmod -R 777 /var/www/html/pub
RUN chmod -R 777 /var/www/html/generated
RUN chmod -R 777 /var/www/html/var
RUN apt install composer -y
RUN composer -V
RUN cd /var/www/html/magento/ && composer install -v
ENTRYPOINT service apache2 restart && bash

