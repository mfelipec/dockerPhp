FROM debian:stable
LABEL maintainer="Marcus Felipe <mfelipec@gmail.com>"

## run apt-get update
RUN echo -e "\n\n------ INSTALANDO PACOTES PHP ------\n"; \
    apt-get update -yqq && \
    apt-get install aptitude curl gnupg php7.3 php7.3-curl php7.3-gd \
    php7.3-pgsql php7.3-mysql php7.3-odbc php7.3-xdebug \
    php7.3-xml php7.3-soap php7.3-pdo-pgsql php7.3-mbstring \
    php7.3-ldap php7.3-zip php7.3-intl php7.3-xmlrpc php-pecl-http \
    php-pear php7.3-dev php-pecl-http-dev apt-utils -yqq


RUN echo -e "\n\n------ INSTALANDO COMPOSER ------\n"; \
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php --install-dir=bin --filename=composer
RUN php -r "unlink('composer-setup.php');"

RUN echo -e "\n\n------ INSTALANDO LARAVEL ------\n"
RUN composer global require laravel/installer

ARG DOCUMENT_ROOT=/var/www/

EXPOSE 80 443

WORKDIR /var/www/

COPY ./sites/default.apache.conf /etc/apache2/sites-available/
COPY ./sites/000-default.conf /etc/apache2/sites-available/
COPY vhost.conf /etc/apache2/sites-enabled/vhosts.conf

COPY phpinfo.php /var/www/phpinfo.php


RUN echo -e "\n\n------ INSTALANDO MSSQL ------\n"
RUN set -eux; \
    curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - && \
    curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list && \
    apt-get update -yqq && \
    ACCEPT_EULA=Y apt-get install -y msodbcsql17 mssql-tools unixodbc \
        libcrypto++6 libcrypto++-dev unixodbc-dev libgss3 odbcinst locales && \
    echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile && \
    echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc && \
    ln -sfn /opt/mssql-tools/bin/sqlcmd /usr/bin/sqlcmd && \
    ln -sfn /opt/mssql-tools/bin/bcp /usr/bin/bcp && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen && \
    pecl install sqlsrv pdo_sqlsrv

COPY sqlsrv.ini /etc/php/7.3/mods-available/sqlsrv.ini
COPY pdo_sqlsrv.ini /etc/php/7.3/mods-available/pdo_sqlsrv.ini
RUN phpenmod sqlsrv pdo_sqlsrv && \
    php -m | grep -q 'sqlsrv' \
    && php -m | grep -q 'pdo_sqlsrv'


RUN apt-get clean autoclean && apt-get autoremove -y

CMD echo "ServerName localhost" >> /etc/apache2/apache2.conf
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

RUN mkdir /tmp/moodledata
RUN chmod 777 /tmp/moodledata