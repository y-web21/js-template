FROM php:8.1-fpm

ARG LARAVEL_VER
ARG NODE_VER=16.14.2 # LTS
ARG UID=1000
ARG USER=user
ARG USER_PASSWORD=user
ARG INSTALL_XDEBUG=false

COPY ./php.ini /usr/local/etc/php/

# locale
RUN apt-get update && apt-get install -y locales && locale-gen C.UTF-8
ENV LANG=C.UTF-8

# install Composer
RUN curl -sS http://getcomposer.org/installer | php -- --2 --filename=composer --install-dir=/usr/bin/
# ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /home/${USER}/.composer
ENV PATH $PATH:$COMPOSER_HOME/vendor/bin

# laravel required packages etc.
RUN apt-get update && apt-get install -y git \
    vim \
    zip \
    unzip \
    procps

# https://github.com/mlocati/docker-php-extension-installer
RUN docker-php-ext-install pdo_mysql

# install and enable xdebug
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
      pecl install xdebug-3.1.3 && docker-php-ext-enable xdebug ;fi

# install nodejs npm
RUN apt-get install -y npm \
    && npm install -g n \
    && n ${NODE_VER} \
    && apt purge -y nodejs npm

# add general user
RUN apt-get install -y sudo \
  && useradd --uid ${UID} --groups sudo ${USER} --user-group --create-home --shell /bin/bash \
  && echo ${USER}:${USER_PASSWORD} | chpasswd \
  && echo root:root | chpasswd \
  && echo "%${USER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER ${USER}

COPY ./userhome/* /home/${USER}/
COPY ./install_laravel.bash /home/${USER}/
ENV LARAVEL_VER=${LARAVEL_VER}
ENV USER=${USER}
