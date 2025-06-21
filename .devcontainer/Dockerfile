FROM php:8.2-apache

# Instalar dependencias del sistema operativo y herramientas necesarias
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
    && rm -rf /var/lib/apt/lists/* # Limpiar caché de apt

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

# Crear usuario appuser (opcional pero recomendado para seguridad y permisos)
ARG UID=1000
ARG GID=1000
RUN groupadd -g ${GID} appuser && \
    useradd -u ${UID} -g appuser -m appuser

# Cambiar al usuario appuser para las operaciones siguientes (mejor seguridad)
USER appuser

# Crear el directorio de trabajo dentro del contenedor
RUN mkdir -p /var/www

# Establecer el directorio de trabajo
WORKDIR /var/www

# --- ¡NUEVAS LÍNEAS CRÍTICAS! ---
# Copiar composer.json y composer.lock primero para aprovechar el caché de Docker.
# Las rutas son relativas al contexto de construcción (.devcontainer/).
# '..' sube un nivel a la raíz del repositorio, luego 'laravel/' accede a la carpeta del código.
COPY ../laravel/composer.json ../laravel/composer.lock ./

# Instalar las dependencias de Composer. Esto ocurre durante la construcción de la imagen.
RUN composer install --no-dev --optimize-autoloader --no-scripts

# Copiar el resto de los archivos de la aplicación Laravel.
# Copia todo el contenido de la carpeta 'laravel' del repositorio a /var/www del contenedor.
COPY ../laravel/ . /var/www/

# Ajustar permisos para los directorios 'storage' y 'bootstrap/cache' de Laravel.
# Estos directorios necesitan ser escribibles por el servidor web (Apache en este caso).
RUN chmod -R 777 storage bootstrap/cache

# Exponer el puerto por el que Apache/Laravel servirá la aplicación.
# Por defecto, Apache en esta imagen escucha en el puerto 80, pero Laravel servirá en 8000.
# Si estás usando php artisan serve en CMD, el puerto 8000 está bien.
EXPOSE 8000

# Comando para iniciar el servidor de desarrollo de Laravel.
# Esta imagen base de Apache ya configura Apache, así que podrías necesitar ajustar esto
# o usar CMD ["apache2-foreground"] si tu Laravel está configurado para Apache.
# Para desarrollo con `php artisan serve`, esta CMD es correcta.
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]