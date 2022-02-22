FROM cimg/base:stable

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

ENV PHP_MINOR 7.3

RUN sudo add-apt-repository -y ppa:ondrej/php && sudo apt-get update

# Install PHP and some PHP extensions
RUN sudo apt-get install -y \
		php${PHP_MINOR} \
		php${PHP_MINOR}-dev \
		php${PHP_MINOR}-apcu \
		php${PHP_MINOR}-bcmath \
		php${PHP_MINOR}-curl \
		php${PHP_MINOR}-gd \
		php${PHP_MINOR}-intl \
		php${PHP_MINOR}-json \
		php${PHP_MINOR}-mbstring \
		php${PHP_MINOR}-mysql \
		php${PHP_MINOR}-pgsql \
		php${PHP_MINOR}-sqlite \
		php${PHP_MINOR}-xml \
		php${PHP_MINOR}-zip

# Install the PHP package manager Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
	php -r "unlink('composer-setup.php');" && \
	composer --version
ENV PATH /home/circleci/.config/composer/vendor/bin:/home/circleci/.composer/vendor/bin:$PATH
