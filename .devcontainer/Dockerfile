# Laravel-React-DvEnvironment/.devcontainer/Dockerfile

# Usar la imagen base de PHP con Apache que estás usando
FROM php:8.2-apache

# Instalar dependencias del sistema operativo y herramientas
RUN apt-get update && apt-get install -y \
    libzip-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libxml2-dev \
    libxslt1-dev \
    libicu-dev \
    libonig-dev \
    libpq-dev \
    zlib1g-dev \
    git \
    unzip \
    wget \
    && rm -rf /var/lib/apt/lists/*

# Instalar extensiones PHP necesarias para Laravel
RUN docker-php-ext-install \
    gd \
    pdo_pgsql \
    mbstring \
    bcmath \
    exif \
    pcntl \
    zip \
    intl \
    xml

# Instalar Composer globalmente en la imagen
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Crear usuario appuser
ARG UID=1000
ARG GID=1000
RUN groupadd -g ${GID} appuser && \
    useradd -u ${UID} -g appuser -m appuser

# Cambiar al usuario appuser
USER appuser

# Crear el directorio de trabajo dentro del contenedor
RUN mkdir -p /var/www

# Establecer el directorio de trabajo
WORKDIR /var/www

# Copiar composer.json y composer.lock desde la carpeta 'laravel' del repositorio.
# La ruta `../laravel/` es correcta.
COPY ../laravel/composer.json ../laravel/composer.lock ./

# Instalar las dependencias de Composer durante la construcción de la imagen.
RUN composer install --no-dev --optimize-autoloader --no-scripts

# --- ¡LÍNEA CORREGIDA! ---
# Copiar el RESTO del CONTENIDO de la carpeta 'laravel' (indicado por el './' al final del origen)
# a /var/www dentro del contenedor.
COPY ../laravel/. /var/www

# Ajustar permisos para los directorios 'storage' y 'bootstrap/cache' de Laravel.
RUN chmod -R 777 storage bootstrap/cache

# Exponer el puerto
EXPOSE 8000

# Comando para iniciar el servidor de desarrollo de Laravel
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]