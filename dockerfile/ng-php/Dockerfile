FROM centos:7

LABEL maintainer="HongX <hx666y@163.com>"

ENV NGINX_VERSION 1.15.5
ENV PHP_VERSION 7.3.3

RUN yum install epel-release -y \
&& yum install -y gcc gcc-c++ make gd-devel libxml2-devel \
        libcurl-devel libjpeg-devel libpng-devel openssl-devel \
        libmcrypt-devel libxslt-devel libtidy-devel autoconf \
        iproute net-tools telnet wget openssl-devel pcre-devel \
&& yum clean all \
&& rm -rf /var/cache/yum/* 

RUN CONFIG="\
        --prefix=/usr/local/php --with-fpm-user=www --with-fpm-group=www \
        --with-config-file-path=/usr/local/php/etc \
        --enable-fpm --enable-opcache \
        --with-mysqli --with-pdo-mysql --with-mhash \
        --with-openssl --with-zlib --with-curl --with-gd \
        --with-jpeg-dir --with-png-dir --with-freetype-dir \
        --enable-mbstring --disable-phar \
" \
&& groupadd www \
&& useradd -s /sbin/nologin -g www www \
&& cd /usr/local/src/ && wget http://docs.php.net/distributions/php-${PHP_VERSION}.tar.gz \
&& tar zxf php-${PHP_VERSION}.tar.gz && cd php-${PHP_VERSION} \
&& ./configure ${CONFIG} \
&& make -j2 \
&& make install \
&& cp php.ini-production /usr/local/php/etc/php.ini \
&& cd / && rm -rf /usr/local/src/* \
&& ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
COPY php-fpm.conf /usr/local/php/etc/

RUN CONFIG="\
	--prefix=/usr/local/nginx \
	--user=www \
	--group=www \
	--with-http_stub_status_module \
	--with-http_ssl_module \
" \
&& cd /usr/local/src && wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz \
&& tar xf nginx-${NGINX_VERSION}.tar.gz && cd nginx-${NGINX_VERSION} \
&& ./configure ${CONFIG} \
&& make -j2 \
&& make install \
&& rm -rf /usr/local/nginx/html/* \
&& echo "ok" >> /usr/local/nginx/html/status.html \
&& ln -sfT /dev/stdout "/usr/local/nginx/logs/access.log" \
&& mkdir -pv /usr/local/nginx/conf/vhost \
&& cd / && rm -rf /usr/local/src/* 
COPY nginx.conf /usr/local/nginx/conf/nginx.conf

ENV PATH $PATH:/usr/local/php/sbin:/usr/local/nginx/sbin
COPY docker-php-entrypoint /usr/bin/
ENTRYPOINT ["docker-php-entrypoint"]
WORKDIR /usr/local
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

