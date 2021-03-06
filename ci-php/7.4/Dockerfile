FROM cimg/base:stable

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

ENV PHP_MINOR 7.4

RUN sudo add-apt-repository -y ppa:ondrej/php
RUN sudo apt-get install -y php${PHP_MINOR} php${PHP_MINOR}-dev

# Pre-install a few very popular PHP extensions
RUN sudo apt-get update && sudo apt-get install -y \
		php$PHP_MINOR-bcmath \
		php$PHP_MINOR-curl \
		php$PHP_MINOR-gd \
		php$PHP_MINOR-intl \
		php$PHP_MINOR-json \
		php$PHP_MINOR-mbstring \
		php$PHP_MINOR-mysql \
		php$PHP_MINOR-pgsql \
		php$PHP_MINOR-sqlite \
		php$PHP_MINOR-xdebug \
		php$PHP_MINOR-xml \
		php$PHP_MINOR-zip

# Install mssql tools and odbc libs
RUN curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list | sudo tee -a /etc/apt/sources.list
RUN sudo apt-get update && sudo ACCEPT_EULA=Y apt-get install -y \
		mssql-tools \
		unixodbc \
		unixodbc-dev \
		odbcinst1debian2 \
		libodbc1

# Install Oracle tools and libs
RUN sudo apt-get install alien libaio1
RUN curl --output oracle-instantclient-basiclite-21.1.0.0.0-1.x86_64.rpm https://download.oracle.com/otn_software/linux/instantclient/211000/oracle-instantclient-basiclite-21.1.0.0.0-1.x86_64.rpm
RUN curl --output oracle-instantclient-sqlplus-21.1.0.0.0-1.x86_64.rpm https://download.oracle.com/otn_software/linux/instantclient/211000/oracle-instantclient-sqlplus-21.1.0.0.0-1.x86_64.rpm
RUN curl --output oracle-instantclient-devel-21.1.0.0.0-1.x86_64.rpm https://download.oracle.com/otn_software/linux/instantclient/211000/oracle-instantclient-devel-21.1.0.0.0-1.x86_64.rpm
RUN sudo alien -i oracle-instantclient-basiclite-21.1.0.0.0-1.x86_64.rpm
RUN sudo alien -i oracle-instantclient-sqlplus-21.1.0.0.0-1.x86_64.rpm
RUN sudo alien -i oracle-instantclient-devel-21.1.0.0.0-1.x86_64.rpm
RUN sudo echo -e "/usr/lib/oracle/21.1/client64/lib/" | sudo tee /etc/ld.so.conf.d/oracle.conf
RUN sudo chmod o+r /etc/ld.so.conf.d/oracle.conf
RUN sudo ldconfig
RUN export ORACLE_HOME=/usr/lib/oracle/21.1/client64
RUN export PATH=$PATH:$ORACLE_HOME/bin
RUN rm oracle-*.rpm

# Install DB2 libs
RUN sudo apt-get install ksh php-pear
RUN curl --output /tmp/x64_v10.5fp8_linuxx64_dsdriver.tar.gz http://cdn1.netmake.com.br/download/Conexao/DB2/Linux/x64_v10.5fp8_linuxx64_dsdriver.tar.gz
RUN sudo mkdir -p /opt/ibm
RUN sudo tar xf /tmp/x64_v10.5fp8_linuxx64_dsdriver.tar.gz -C /opt/ibm
RUN sudo ksh /opt/ibm/dsdriver/installDSDriver
RUN rm /tmp/x64_v10.5fp8_linuxx64_dsdriver.tar.gz

# Install PDO drivers
RUN sudo pecl channel-update pecl.php.net
RUN sudo IBM_DB_HOME=/opt/ibm/dsdriver pecl install ibm_db2
RUN echo -e "; priority=20\nextension=ibm_db2.so" | sudo tee /etc/php/$PHP_MINOR/mods-available/ibm_db2.ini
RUN sudo phpenmod ibm_db2
RUN sudo pecl install oci8-2.2.0
RUN echo -e "; priority=20\nextension=oci8.so" | sudo tee /etc/php/$PHP_MINOR/mods-available/oci8.ini
RUN sudo phpenmod oci8
RUN sudo pecl install pdo_sqlsrv
RUN echo -e "; priority=20\nextension=pdo_sqlsrv" | sudo tee /etc/php/$PHP_MINOR/mods-available/pdo_sqlsrv.ini
RUN sudo phpenmod pdo_sqlsrv

# Install the PHP package manager Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
	php -r "unlink('composer-setup.php');" && \
	composer --version
ENV PATH /home/circleci/.config/composer/vendor/bin:/home/circleci/.composer/vendor/bin:$PATH
