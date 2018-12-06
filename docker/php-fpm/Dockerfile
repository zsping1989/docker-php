FROM centos:7

MAINTAINER 214986304@qq.com

# Install some must-haves
RUN yum -y install vim wget sendmail
RUN yum -y install libtool make automake autoconf nasm libpng-static

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


# Final update and clean up
RUN yum -y update
RUN yum clean all

# 定义工作目录
WORKDIR /var/www/laravel
# 将挂载过来的代码全部复制到容器中方便镜像打包到时候将代码一起打包
COPY . /var/www/laravel


# 暴露端口
EXPOSE 9000


CMD ["php-fpm", "-F", "-O"]
