FROM cimg/base:edge

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

ENV PHP_MINOR 8.3

RUN sudo add-apt-repository -s -y ppa:ondrej/php

# Install PHP and some PHP extensions
RUN sudo apt-get install -y \
		php${PHP_MINOR} \
		php${PHP_MINOR}-dev \
		php${PHP_MINOR}-apcu \
		php${PHP_MINOR}-bcmath \
		php${PHP_MINOR}-curl \
		php${PHP_MINOR}-gd \
		php${PHP_MINOR}-intl \
		php${PHP_MINOR}-mbstring \
		php${PHP_MINOR}-mysql \
		php${PHP_MINOR}-pgsql \
		php${PHP_MINOR}-sqlite \
		php${PHP_MINOR}-xdebug \
		php${PHP_MINOR}-xml \
		php${PHP_MINOR}-zip

# Make sure to use the correct PHP version
RUN sudo update-alternatives --set php /usr/bin/php${PHP_MINOR}

# Install mssql tools and odbc libs
RUN curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/22.04/prod.list | sudo tee -a /etc/apt/sources.list
RUN sudo apt-get update && sudo ACCEPT_EULA=Y apt-get install -y \
		mssql-tools \
		unixodbc \
		unixodbc-dev \
		odbcinst1debian2 \
		libodbc1

# Install Oracle tools and libs
RUN sudo apt-get install alien libaio1
RUN curl --output oracle-instantclient-basiclite-linuxx64.rpm https://download.oracle.com/otn_software/linux/instantclient/oracle-instantclient-basiclite-linuxx64.rpm
# RUN curl --output oracle-instantclient-sqlplus-linuxx64.rpm https://download.oracle.com/otn_software/linux/instantclient/oracle-instantclient-sqlplus-linuxx64.rpm
RUN curl --output oracle-instantclient-devel-linuxx64.rpm https://download.oracle.com/otn_software/linux/instantclient/oracle-instantclient-devel-linuxx64.rpm
RUN sudo alien -i oracle-instantclient-basiclite-linuxx64.rpm
# RUN sudo alien -i oracle-instantclient-sqlplus-linuxx64.rpm
RUN sudo alien -i oracle-instantclient-devel-linuxx64.rpm
RUN export ORACLE_HOME=`ls -d /usr/lib/oracle/*/client64`
RUN echo "'$ORACLE_HOME'"
RUN sudo echo -e "$ORACLE_HOME/lib/" | sudo tee /etc/ld.so.conf.d/oracle.conf
RUN sudo chmod o+r /etc/ld.so.conf.d/oracle.conf
RUN sudo ldconfig
RUN export PATH=$PATH:$ORACLE_HOME/bin
RUN rm oracle-*.rpm

# Install DB2 libs
RUN sudo apt-get install ksh php-pear
RUN curl --output /tmp/linuxx64_odbc_cli.tar.gz https://public.dhe.ibm.com/ibmdl/export/pub/software/data/db2/drivers/odbc_cli/linuxx64_odbc_cli.tar.gz
RUN sudo mkdir -p /opt/ibm
RUN sudo tar xf /tmp/linuxx64_odbc_cli.tar.gz -C /opt/ibm
RUN rm /tmp/linuxx64_odbc_cli.tar.gz

# Install PDO drivers
RUN sudo pecl channel-update pecl.php.net
RUN sudo IBM_DB_HOME=/opt/ibm/clidriver pecl install ibm_db2
RUN echo -e "; priority=20\nextension=ibm_db2.so" | sudo tee /etc/php/$PHP_MINOR/mods-available/ibm_db2.ini
RUN sudo phpenmod ibm_db2
RUN sudo pecl install oci8
RUN echo -e "; priority=20\nextension=oci8.so" | sudo tee /etc/php/$PHP_MINOR/mods-available/oci8.ini
RUN sudo phpenmod oci8
RUN sudo pecl install sqlsrv-beta
RUN echo -e "; priority=20\nextension=sqlsrv" | sudo tee /etc/php/$PHP_MINOR/mods-available/sqlsrv.ini
RUN sudo phpenmod sqlsrv
RUN sudo pecl install pdo_sqlsrv-beta
RUN echo -e "; priority=20\nextension=pdo_sqlsrv" | sudo tee /etc/php/$PHP_MINOR/mods-available/pdo_sqlsrv.ini
RUN sudo phpenmod pdo_sqlsrv

RUN apt-get source php${PHP_MINOR}
RUN cd `find php${PHP_MINOR}*/ext/pdo_oci -mindepth 0 -maxdepth 0 -type d` && phpize
RUN cd `find php${PHP_MINOR}*/ext/pdo_oci -mindepth 0 -maxdepth 0 -type d` && ./configure --with-pdo-oci=instantclient,/usr/lib/oracle/24/client64/lib/
RUN cd `find php${PHP_MINOR}*/ext/pdo_oci -mindepth 0 -maxdepth 0 -type d` && make
RUN cd `find php${PHP_MINOR}*/ext/pdo_oci -mindepth 0 -maxdepth 0 -type d` && sudo make install
RUN echo -e "; priority=20\nextension=pdo_oci.so" | sudo tee /etc/php/$PHP_MINOR/mods-available/pdo_oci.ini
RUN sudo phpenmod pdo_oci
RUN rm -rf php${PHP_MINOR}*

# Install the PHP package manager Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
	php -r "unlink('composer-setup.php');" && \
	composer --version
ENV PATH /home/circleci/.config/composer/vendor/bin:/home/circleci/.composer/vendor/bin:$PATH
