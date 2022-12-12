FROM php:8.1-fpm-alpine

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

ENV PHP_MINOR 8.2

RUN set -eux

RUN apk add --no-cache imagemagick
RUN apk add --no-cache --virtual .build-deps \
		$PHPIZE_DEPS \
		freetype-dev \
		icu-dev \
		imagemagick-dev \
		libjpeg-turbo-dev \
		libpng-dev \
		libwebp-dev \
		libzip-dev

RUN apk add --no-cache --virtual .build-deps \
		libcurl \
		zlib \
		zlib-dev

RUN docker-php-ext-configure gd \
		--with-freetype \
		--with-jpeg \
		--with-webp

RUN docker-php-ext-install -j "$(nproc)" \
		bcmath \
		ctype \
		curl \
		dom \
		fileinfo \
		gd \
		gettext \
		gmp \
		iconv \
		intl \
		json \
		mbstring \
		opcache \
		pcntl \
		pcre \
		pdo \
		pdo_mysql \
		pdo_pgsql \
		spl \
		standard \
		xml \
		zip

RUN pecl install imagick
RUN docker-php-ext-enable imagick
RUN rm -r /tmp/pear

RUN docker-php-ext-enable opcache;
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=10000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

RUN { \
		echo 'error_reporting = E_ERROR | E_WARNING | E_PARSE | E_CORE_ERROR | E_CORE_WARNING | E_COMPILE_ERROR | E_COMPILE_WARNING | E_RECOVERABLE_ERROR'; \
		echo 'display_errors = Off'; \
		echo 'display_startup_errors = Off'; \
		echo 'log_errors = On'; \
		echo 'error_log = /dev/stderr'; \
		echo 'log_errors_max_len = 1024'; \
		echo 'ignore_repeated_errors = On'; \
		echo 'ignore_repeated_source = Off'; \
		echo 'html_errors = Off'; \
	} > /usr/local/etc/php/conf.d/error-logging.ini

# Install the PHP package manager Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
	php -r "unlink('composer-setup.php');" && \
	composer --version

RUN echo '<?php phpinfo' > /var/www/html/index.php

VOLUME /var/www/html

CMD ["php-fpm"]