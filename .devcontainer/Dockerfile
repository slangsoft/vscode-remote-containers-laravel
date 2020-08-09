FROM centos:8

LABEL maintainer="slangsoft"
LABEL version="1.0"
LABEL description="Laravel sample project for trying out the VS Code Remote - Containers extension."

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG PATH_LARAVEL=/var/www/html

# Basic settings
RUN set -x \
    && dnf -y upgrade \
    && dnf -y install sudo dnf-utils wget git \
    && groupadd --gid $USER_GID $USERNAME \
    && useradd --shell /bin/bash --uid $USER_UID --gid $USER_GID --create-home $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Install nginx
RUN  dnf -y install nginx

# Install php
RUN set -x \
    && dnf module reset php \
    && dnf -y install epel-release \
    && dnf -y install https://rpms.remirepo.net/enterprise/remi-release-8.rpm \
    && dnf -y module install php:remi-7.4

# Install Laravel
EXPOSE 8000
RUN set -x \
    && dnf -y install php-mcrypt php-pdo php-bcmath php-tokenizer php-xml php-mysqlnd php-gd php-intl php-zip php-opcache php-pecl-xdebug \
    && wget https://getcomposer.org/installer -O composer-installer.php \
    && php composer-installer.php --filename=composer --install-dir=/usr/local/bin \
    && composer self-update \
    && composer create-project laravel/laravel $PATH_LARAVEL --prefer-dist \
    && groupadd laravel \
    && gpasswd -a $USERNAME laravel \
    && gpasswd -a nginx laravel \
    && find $PATH_LARAVEL -type d -exec chmod 775 \{\} \; \
    && find $PATH_LARAVEL -type f -exec chmod 664 \{\} \; \
    && chown -R :$USERNAME $PATH_LARAVEL \
    && find $PATH_LARAVEL -type d -exec chmod g+s \{\} \; \
    && setfacl -R -d -m g::rwx $PATH_LARAVEL

# Install MongoDB
EXPOSE 27017
COPY ./mongodb-org.repo /etc/yum.repos.d/
RUN dnf -y install mongodb-org

# Working directory setting
WORKDIR $PATH_LARAVEL
