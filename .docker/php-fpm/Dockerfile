FROM bitnami/php-fpm:7.3-prod AS php-fpm
LABEL maintainer="Pierre Ribault <contact@pierreribault.fr>"

# This image is designed for Laravel requirements
# Extensions list activated
#
# -> BCMath PHP Extension
# -> Ctype PHP Extension
# -> JSON PHP Extension
# -> Mbstring PHP Extension
# -> OpenSSL PHP Extension
# -> PDO PHP Extension
# -> Tokenizer PHP Extension
# -> XML PHP Extension

# Installation of requirements
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    autoconf \
    build-essential \
    locales \
    unzip \
    supervisor \
    && apt-get clean \
    && rm -rf apt_list.txt \
    && rm -rf /var/lib/apt/lists/*

# Set the local
RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && sed -i -e 's/# fr_FR.UTF-8 UTF-8/fr_FR.UTF-8 UTF-8/' /etc/locale.gen \
    && locale-gen

# Configuration of ENV
ENV LANG="fr_FR.UTF-8" \
    LANGUAGE="fr_FR:fr" \
    LC_ALL="fr_FR.UTF-8" \
    XDG_CONFIG_HOME=/app \
    WAIT_VERSION="2.7.3"

# Add Docker-compose-wait
ADD https://github.com/ufoscout/docker-compose-wait/releases/download/$WAIT_VERSION/wait /wait
RUN chmod +x /wait

# Configuration of STDOUT/STDERROR
RUN ln -sf /dev/stdout /opt/bitnami/php/logs/php-fpm.log

# Configuration of PHP-FPM
COPY --chown=1001:1001 ./.docker/php-fpm/php-fpm/php-fpm.conf /opt/bitnami/php/etc/php-fpm.d/www.conf
COPY ./.docker/php-fpm/php-fpm/php.conf /opt/bitnami/php/etc/conf.d/custom.ini

# Configuration of Supervisor
COPY ./.docker/php-fpm/supervisord/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Configuration of Entrypoint
COPY ./.docker/php-fpm/docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh

# Copy project
COPY ./app /app
RUN chown -R www-data:www-data /app

# Modify php-fpm permissions
RUN chown www-data:daemon /opt/bitnami/php/tmp \
    && chmod 775 /opt/bitnami/php/tmp

USER www-data

CMD ["/docker-entrypoint.sh"]
