FROM centos:7
MAINTAINER mkrakauer@rioseo.com

#
# Install our main components and dependencies
#
RUN yum -y --setopt=tsflags=nodocs install wget \
	&& wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
	&& wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm \
	&& wget https://dl.yarnpkg.com/rpm/yarn.repo -O /etc/yum.repos.d/yarn.repo \
	&& curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.rpm.sh | bash \
	&& rpm -Uvh remi-release-7.rpm epel-release-latest-7.noarch.rpm \
	&& yum-config-manager --enable remi-php71 \
	&& yum -y --setopt=tsflags=nodocs update

RUN yum -y --setopt=tsflags=nodocs install dos2unix git git-lfs httpd mysql npm yarn openssl vim-enhanced \
	php php-cli php-gd php-intl php-mbstring php-mysqlnd php-opcache php-pdo php-soap php-xml php-zip \
	php-pecl-memcached php-pecl-rdkafka php-pecl-ssh2 php-pecl-xdebug \
	php-pear php-devel gcc make openssl-devel \
	&& rm -rf /var/cache/yum/* \
	&& yum clean all

#
# Compile legacy mongo library
#
RUN pecl install mongodb-1.2.0 \
    && echo "extension=mongodb.so" > /etc/php.d/20-mongodb.ini

#
# Intall composer
#
RUN curl -sS https://getcomposer.org/installer | php \
	&& mv composer.phar /usr/local/bin/composer

#
# Install phpunit
#
RUN curl -L -o phpunit https://phar.phpunit.de/phpunit-7.phar \
	&& chmod +x phpunit \
	&& mv phpunit /usr/local/bin/

#
# Install grunt
#
RUN npm install -g grunt-cli

#
# Install git LFS
#
RUN git lfs install

#
# Apache Configuration
#
#COPY httpd.conf /etc/httpd/conf/httpd.conf
#COPY ssl.conf /etc/httpd/conf.d/ssl.conf

#
# PHP Configuration
#
COPY php.ini /etc/php.ini

#
# Simple startup script to avoid some issues observed with container restart 
#
COPY run-httpd.sh /run-httpd.sh


# ------------------------
# RLS Setup
# ------------------------
RUN mkdir -p /var/www/html/dev \
	&& chmod -v +x /run-httpd.sh


ENV PHP_OPTIONS_DATE_TIMEZONE UTC

EXPOSE 80 443

CMD ["/run-httpd.sh"]
