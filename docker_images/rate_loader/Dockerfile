FROM ubuntu:20.04

LABEL maintainer="parser instance "

ARG DATABASE_URL
ARG APP_ENV

ENV PHP_VERSION=8.1 \
    USER=magento \
    XDEBUG_MODE=off \
    TZ=Europe/Kiev
RUN apt update
RUN apt install -y git vim
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
    mkdir -p /var/www/vhosts && \
    chown -R $USER:$USER /var/www/vhosts && \
    mkdir -p /home/$USER/.composer && \
    mkdir -p /home/$USER/profiler && \
    chown -R $USER:$USER /home/$USER && \
    chown -R $USER:$USER /var/www/vhosts

RUN cd /var/www/vhosts

RUN git clone https://github.com/TheTangro/credo-currency-rate-notifier.git notifier

RUN chown -R $USER:$USER notifier/

USER $USER

RUN cd notifier && composer install --optimize-autoloader --no-interaction --no-scripts

RUN echo "DATABASE_URL=$DATABASE_URL" >> ./notifier/.env.local && echo "APP_ENV=$APP_ENV" >> ./notifier/.env.local

#RUN cd / && /usr/bin/php ./notifier/bin/console doctrine:database:create --no-interaction  \
#    && /usr/bin/php ./notifier/bin/console doctrine:migrations:migrate --no-interaction
#RUN /usr/bin/php ./notifier/bin/console proxy-list:load

CMD ["/usr/bin/php", "/notifier/bin/console", "runtime:execute"]