FROM cimg/base:stable

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

ENV PHP_MINOR 7.4

RUN sudo add-apt-repository -y ppa:ondrej/php && \
	sudo apt-get install -y php${PHP_MINOR} php${PHP_MINOR}-dev && \
	sudo rm -rf /var/lib/apt/lists/*

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
		php$PHP_MINOR-xdebug \
		php$PHP_MINOR-xml \
		php$PHP_MINOR-zip \
	&& \
	sudo rm -rf /var/lib/apt/lists/*

RUN curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list | sudo tee -a /etc/apt/sources.list
RUN sudo apt-get update && sudo ACCEPT_EULA=Y apt-get install -y \
		mssql-tools \
		unixodbc \
		unixodbc-dev \
		odbcinst1debian2 \
		libodbc1

RUN sudo pecl channel-update pecl.php.net && sudo pecl install pdo_sqlsrv
RUN echo -e "; priority=20\nextension=pdo_sqlsrv" | sudo tee /etc/php/$PHP_MINOR/mods-available/pdo_sqlsrv.ini
RUN sudo phpenmod pdo_sqlsrv

# Install the PHP package manager Composer
RUN sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
	sudo php -r "unlink('composer-setup.php');" && \
	composer --version
ENV PATH /home/circleci/.config/composer/vendor/bin:/home/circleci/.composer/vendor/bin:$PATH
