FROM ubuntu

ENV TERM=xterm
ARG DEBIAN_FRONTEND=noninteractive


RUN apt update -y && apt -y install software-properties-common
RUN apt-add-repository ppa:ondrej/php && apt update -y
RUN apt install -y php7.4-mbstring php7.4 php7.4-tokenizer php7.4-json php7.4-zip php7.4-fpm \
    php7.4-gd php7.4-xml php7.4-bcmath php7.4-curl php7.4-dom php7.4-mysql php7.4-cli \
    unzip git tzdata curl composer nginx bash openssl
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

WORKDIR /var/www/html

COPY . .

RUN cp .env.example .env
RUN mkdir -p /run/php

RUN cp nginx/apps.conf /etc/nginx/sites-enabled/default
RUN composer update && composer dump-autoload
RUN composer install --no-dev --optimize-autoloader
RUN composer require fideloper/proxy
RUN php artisan optimize:clear
RUN php artisan view:clear
RUN php artisan key:generate
RUN chmod -R 777 storage bootstrap/cache

#CMD php-fpm7.4 -D && nginx -g "daemon off;"
CMD php artisan serve