FROM php:8.3-apache

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    zlib1g-dev \
    libicu-dev \
    libzip-dev \
    g++ \
    nodejs \
    npm \
    w3m 

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd intl zip

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www/html

# Copy existing application directory contents
COPY ./VisionHub/ .

RUN composer install
RUN npm install
RUN npm run build
RUN php artisan key:generate
#RUN php artisan migrate
#RUN php artisan db:seed
RUN php artisan storage:link

# Expose port 80
EXPOSE 80

ENV APACHE_DOCUMENT_ROOT /var/www/html/public/
RUN sed -ri -e "s!/var/www/html!/var/www/html/public/!g" /etc/apache2/sites-available/*.conf
RUN sed -ri -e "s!/var/www/!/var/www/html/public/!g" /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf
RUN sed -ri -e "s!/var/www/html/public/!/var/www/html/public\n   <Directory /var/www/html/public>\n        Options Indexes FollowSymLinks\n        AllowOverride All\n        Require all granted\n    </Directory>!g" /etc/apache2/sites-available/*.conf

RUN a2enmod rewrite

# Start Apache in the foreground
CMD ["apache2-foreground"]
