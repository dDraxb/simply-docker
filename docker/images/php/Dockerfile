FROM php:7.4-fpm

ENV DEBIAN_FRONTEND noninteractive
ENV PHP_MEMORY_LIMIT=1G

# Localizations
RUN apt-get update -qq  \
    && apt-get install -y  \
      locales -qq  \
    && echo "nl_NL.UTF-8 UTF-8" >> /etc/locale.gen  \
    && echo "en_GB.UTF-8 UTF-8" >> /etc/locale.gen  \
    && locale-gen  \
    && dpkg-reconfigure locales \
	&& rm -r /var/lib/apt/lists/*

# Install basic utilities
RUN apt-get update -qq \
	&& apt-get install -y \
	    $PHPIZE_DEPS \
	    unzip \
	    openssl \
	    wget \
        less \
        supervisor \
    --no-install-recommends \
    && rm -r /var/lib/apt/lists/*

# Install dependencies for GD
RUN apt-get update -qq \
	&& apt-get install -y \
		libpng-dev \
        libjpeg62-turbo-dev \
        libfreetype6-dev \
    --no-install-recommends \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && rm -r /var/lib/apt/lists/*

# Install dependencies for extensions
RUN apt-get update -qq  \
    && apt-get install -y \
        libicu-dev \
        libxml2-dev \
        libgmp3-dev \
        zlib1g-dev \
        libzip-dev \
        libonig-dev \
    --no-install-recommends  \
    && rm -r /var/lib/apt/lists/*

# Run a quick serialization for Redis
RUN docker-php-source extract \
	&& yes | pecl install \
		igbinary \
		redis-5.0.2 \
	&& docker-php-ext-enable \
		igbinary \
		redis \
	&& docker-php-source delete

# Install PHP extensions
RUN docker-php-ext-configure zip \
	&& docker-php-ext-install -j$(nproc)  \
        opcache  \
        pdo  \
        pdo_mysql  \
        mbstring  \
        gmp  \
        tokenizer  \
        xml  \
        json  \
        dom  \
        zip  \
        exif  \
        fileinfo  \
        bcmath  \
        intl

# Install Composer
RUN wget https://raw.githubusercontent.com/composer/getcomposer.org/7bfcc5eaf3af1fe20e172a8676d2fb9bb8162d7e/web/installer \
	  -O - -q | php -- --quiet --install-dir=/bin --filename=composer  \
    && chmod +x /bin/composer  \
    && /bin/composer self-update

# Install NodeJs and NPM
RUN apt-get update -qq  \
    && apt-get install -y \
      nodejs  \
      npm \
    --no-install-recommends \
    && npm install

# Configure Mailhog
RUN apt-get update  \
    && apt-get install -y \
      ca-certificates  \
      curl  \
      git \
    --no-install-recommends --assume-yes --quiet  \
    &&  rm -rf /var/lib/apt/lists/* \
    && curl -Lsf 'https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz' | tar -C '/usr/local' -xvzf -

ENV PATH /usr/local/go/bin:$PATH

RUN go get github.com/mailhog/mhsendmail  \
    && cp /root/go/bin/mhsendmail /usr/bin/mhsendmail \
    && echo 'sendmail_path = /usr/bin/mhsendmail --smtp-addr mailhog:1025' > /usr/local/etc/php/php.ini
