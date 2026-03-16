FROM php:8.3-apache-bookworm

ARG USER=user
ARG PASSWORD=password

RUN sed -i 's|http://deb.debian.org/debian|http://mirror.yandex.ru/debian|g' /etc/apt/sources.list.d/debian.sources && \
    sed -i 's|http://security.debian.org/debian-security|http://mirror.yandex.ru/debian-security|g' /etc/apt/sources.list.d/debian.sources 2>/dev/null || true

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        sudo \
        build-essential \
        libzip-dev \
        libpng-dev \
        libjpeg62-turbo-dev \
        libwebp-dev \
        libxpm-dev \
        libfreetype6 \
        libfreetype6-dev \
        locales \
        zip \
        jpegoptim \
        optipng \
        gifsicle \
        nano \
        unzip \
        git \
        curl \
    || { \
        sed -i 's|mirror.yandex.ru|ftp.de.debian.org|g' /etc/apt/sources.list.d/debian.sources && \
        apt-get update && \
        apt-get install -y --no-install-recommends \
            sudo \
            build-essential \
            libzip-dev \
            libpng-dev \
            libjpeg62-turbo-dev \
            libwebp-dev \
            libxpm-dev \
            libfreetype6 \
            libfreetype6-dev \
            locales \
            zip \
            jpegoptim \
            optipng \
            gifsicle \
            nano \
            unzip \
            git \
            curl; \
    } && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-jpeg --with-freetype && \
    docker-php-ext-install -j$(nproc) pdo_mysql zip exif pcntl mysqli gd

RUN useradd -m -s /bin/bash -p $(openssl passwd -1 $PASSWORD) $USER && \
    usermod -aG sudo $USER && \
    chown -R $USER:$USER /var/www/html && \
    echo "cd /var/www/html" >> /home/$USER/.bashrc

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

RUN a2enmod rewrite

ENTRYPOINT ["/bin/sh", "-c", "chmod 777 -R /var/www/html && apache2-foreground"]