# Choose our base image
FROM serversideup/php:8.5-fpm-nginx

# Switch to root so we can do root things
USER root

# Install the extensionss with root permissions
RUN install-php-extensions intl gd

# Drop back to our unprivileged user
USER www-data
