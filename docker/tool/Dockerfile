FROM centos:7

MAINTAINER 214986304@qq.com

# Install some must-haves
RUN yum -y install vim wget sendmail
RUN yum -y install libtool make automake autoconf nasm libpng-static
RUN yum -y install git
RUN git --version

# Install PHP 7.1 on CentOS
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
	&& rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
RUN yum install yum-utils
RUN yum-config-manager --enable remi-php71

RUN yum -y install php71w \
					         php71w-bcmath \
					         php71w-cli \
					         php71w-common \
					         php71w-curl \
					         php71w-fpm \
					         php71w-gd \
					         php71w-ldap \
					         php71w-imap \
					         php71w-intl \
					         php71w-mbstring \
					         php71w-mcrypt \
					         php71w-mysqlnd \
					         php71w-opcache \
					         php71w-pdo \
					         php71w-pear \
					         php71w-pecl-apcu \
					         php71w-pecl-imagick \
					         php71w-pgsql \
					         php71w-process \
					         php71w-pspell \
					         php71w-recode \
					         php71w-soap \
					         php71w-tidy \
					         php71w-xml

RUN php -v

# Prepare PHP environment
COPY ./php/php-fpm.conf /etc/php-fpm.conf
COPY ./php/www.conf /etc/php-fpm.d/www.conf
COPY ./php/php.ini /usr/local/etc/php/php.ini
COPY ./php/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# 安装composer
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/bin/composer
RUN composer --version
# 设置国内镜像
RUN composer config -g repo.packagist composer https://packagist.phpcomposer.com
# 设置环境变量
RUN echo 'export PATH="$PATH:/root/.composer/vendor/bin"' > /etc/profile
RUN export PATH=$PATH:/root/.composer/vendor/bin
# 安装laravel构建工具
RUN composer global require "laravel/installer"
RUN ln -s /root/.composer/vendor/bin/laravel /usr/bin/laravel
RUN laravel -v
# 安装phpunit自动测试工具
RUN wget http://phar.phpunit.cn/phpunit.phar
RUN chmod +x phpunit.phar
RUN mv ./phpunit.phar /root/.composer/vendor/bin
RUN ln -s /root/.composer/vendor/bin/phpunit.phar /usr/bin/phpunit
RUN phpunit --version

# 安装php代码检查规范
RUN composer global require "squizlabs/php_codesniffer"
RUN php /root/.composer/vendor/bin/phpcs -h

# 安装nodejs
RUN curl -sL https://rpm.nodesource.com/setup_7.x | bash -
RUN yum -y install nodejs
RUN yum list installed nodejs
RUN node -v
# 设置nodejs为国内镜像
RUN npm install -g cnpm --registry=https://registry.npm.taobao.org
RUN cnpm -v

# Final update and clean up
RUN yum -y update
RUN yum clean all

#安装小程序开发工具wepy
RUN cnpm install wepy-cli -g

# 安装vue构建命令
RUN cnpm install -g vue-cli
RUN vue --version

# 安装tarojs用于各小程序，H5开发
RUN cnpm install -g @tarojs/cli
RUN taro --version

RUN cnpm install -g yarn
RUN yarn --version


# Define work directory
WORKDIR /var/www/laravel


