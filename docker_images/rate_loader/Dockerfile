FROM ubuntu:20.04

LABEL maintainer="TheTangro Docker Images Maintainers"

ENV PHP_VERSION=8.1 \
    USER=magento \
    XDEBUG_MODE=off \
    TZ=Europe/Kiev
RUN apt update
RUN apt install -y git
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt install -y software-properties-common
RUN add-apt-repository ppa:ondrej/php
RUN apt update
RUN apt install -y php8.1-cli \
                   php8.1-common  \
                   php8.1-gd \
                   php8.1-intl \
                   php8.1-mbstring \
                   php8.1-zip \
                   php8.1-xml \
                   php8.1-pgsql \
                   php8.1-curl \
                   php8.1-bcmath \
                   php8.1-bcmath \
                   php8.1-iconv
RUN apt install -y curl vim
RUN curl -sS https://getcomposer.org/composer-2.phar --output /usr/local/bin/composer
RUN chmod +x /usr/local/bin/*


RUN useradd -s /bin/bash -m $USER && \
    mkdir -p /home/$USER/.composer && \
    mkdir -p /home/$USER/profiler && \
    chown -R $USER:$USER /home/$USER && \
    chown -R $USER:$USER /var/www/vhosts \

RUN cd /var/www/vhosts

RUN git clone https://github.com/TheTangro/credo-currency-rate-notifier notifier  \
    && cd notifier \
    && composer install --no-dev --optimize-autoloader --no-interaction \
    && /usr/bin/php bin/console cache:clear --no-interaction \
    && /usr/bin/php bin/console cache:warmup --no-interaction

RUN /usr/bin/php bin/console doctrine:migrations:migrate --no-interaction  \
    && /usr/bin/php bin/console proxy-list:load

USER $USER

CMD ["/usr/bin/php", "/var/www/vhosts/notifier/bin/console", "runtime:execute"]
