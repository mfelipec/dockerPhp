FROM debian:jessie
LABEL maintainer="Marcus Felipe <mfelipec@gmail.com>"

ARG DOCUMENT_ROOT=/var/www/html/

## run apt-get update
RUN apt-get update -yqq && \
    apt-get install aptitude php5 php5-gd \
    php5-mcrypt php5-pgsql php5-mysql php5-odbc php5-xdebug -yqq

EXPOSE 80 443

WORKDIR /var/www/html/

COPY vhost.conf /etc/apache2/sites-enabled/vhosts.conf

COPY phpinfo.php /var/www/html/phpinfo.php

CMD echo "ServerName localhost" >> /etc/apache2/apache2.conf
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
