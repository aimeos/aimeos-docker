FROM cimg/base:current-24.04

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

ENV PHP_MINOR=8.5

RUN sudo add-apt-repository -s -y ppa:ondrej/php
RUN sudo apt update

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
#		php${PHP_MINOR}-xdebug \
		php${PHP_MINOR}-xml \
		php${PHP_MINOR}-zip

# Make sure to use the correct PHP version
RUN sudo update-alternatives --set php /usr/bin/php${PHP_MINOR}

# Install mssql tools and odbc libs
RUN curl -sSL -O https://packages.microsoft.com/config/ubuntu/24.04/packages-microsoft-prod.deb
RUN sudo dpkg -i packages-microsoft-prod.deb && rm packages-microsoft-prod.deb
RUN sudo apt-get update && sudo ACCEPT_EULA=Y apt-get install -y \
	mssql-tools18 \
	unixodbc-common \
	unixodbc-dev \
	libodbcinst2 \
	libodbc2

# Install Oracle tools and libs
RUN sudo apt-get install alien libaio1t64
RUN curl --output ~/oracle-instantclient-basiclite-linuxx64.rpm https://download.oracle.com/otn_software/linux/instantclient/oracle-instantclient-basiclite-linuxx64.rpm
RUN curl --output ~/oracle-instantclient-devel-linuxx64.rpm https://download.oracle.com/otn_software/linux/instantclient/oracle-instantclient-devel-linuxx64.rpm
RUN curl --output ~/oracle-instantclient-sqlplus-linuxx64.zip https://download.oracle.com/otn_software/linux/instantclient/instantclient-sqlplus-linuxx64.zip
RUN mkdir ~/oracle; unzip -d ~/oracle ~/oracle-instantclient-sqlplus-linuxx64.zip
RUN sudo alien -i ~/oracle-instantclient-basiclite-linuxx64.rpm
RUN sudo alien -i ~/oracle-instantclient-devel-linuxx64.rpm
RUN sudo echo -e `ls -d /usr/lib/oracle/*/client64/lib` | sudo tee /etc/ld.so.conf.d/oracle.conf
RUN sudo chmod o+r /etc/ld.so.conf.d/oracle.conf
RUN sudo ln -s /usr/lib/x86_64-linux-gnu/libaio.so.1t64 /usr/lib/x86_64-linux-gnu/libaio.so.1
RUN sudo ldconfig
RUN rm ~/oracle-instantclient-*
ENV PATH=$PATH:~/oracle/bin

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
RUN sudo ORACLE_HOME=/usr/lib/oracle/23/client64 CPPFLAGS="-I/usr/include/oracle/23/client64" pecl install oci8
RUN echo -e "; priority=20\nextension=oci8.so" | sudo tee /etc/php/$PHP_MINOR/mods-available/oci8.ini
RUN sudo phpenmod oci8
#RUN sudo ORACLE_HOME=/usr/lib/oracle/23/client64 CPPFLAGS="-I/usr/include/oracle/23/client64" pecl install pdo_oci
#RUN echo -e "; priority=20\nextension=pdo_oci.so" | sudo tee /etc/php/$PHP_MINOR/mods-available/pdo_oci.ini
#RUN sudo phpenmod pdo_oci
#RUN sudo pecl install sqlsrv
#RUN echo -e "; priority=20\nextension=sqlsrv" | sudo tee /etc/php/$PHP_MINOR/mods-available/sqlsrv.ini
#RUN sudo phpenmod sqlsrv
#RUN sudo pecl install pdo_sqlsrv
#RUN echo -e "; priority=20\nextension=pdo_sqlsrv" | sudo tee /etc/php/$PHP_MINOR/mods-available/pdo_sqlsrv.ini
#RUN sudo phpenmod pdo_sqlsrv

# Install the PHP package manager Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
	php -r "unlink('composer-setup.php');" && \
	composer --version
ENV PATH=/home/circleci/.config/composer/vendor/bin:/home/circleci/.composer/vendor/bin:$PATH
