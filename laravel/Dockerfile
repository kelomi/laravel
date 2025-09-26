# laravel/Dockerfile
FROM php:7.4-cli

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libzip-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libpng-dev \
  && docker-php-ext-configure gd --with-jpeg --with-freetype \
  && docker-php-ext-install -j$(nproc) pdo_mysql mbstring exif pcntl bcmath gd zip \
  && pecl install redis && docker-php-ext-enable redis \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

# copy entire app first so composer scripts and autoload have everything
WORKDIR /var/www
COPY . .

# Install composer binary (from official image)
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Ensure directories exist and permissions
RUN mkdir -p storage bootstrap/cache \
  && composer install --no-interaction --prefer-dist --optimize-autoloader --no-scripts || composer install --no-interaction --prefer-dist \
  && php artisan key:generate || true \
  && chown -R www-data:www-data storage bootstrap/cache \
  && chmod -R 0775 storage bootstrap/cache

EXPOSE 8000

# For dev you can use php artisan serve; in production you'd use php-fpm + nginx
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]
