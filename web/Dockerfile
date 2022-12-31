FROM centos:7
RUN yum update -y && yum clean all

# Repository
# EPEL
RUN yum install -y epel-release
# remi
RUN yum -y install http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

# install tool
RUN yum install -y wget git patch vim
RUN yum install -y gcc pcre pcre-devel openssl openssl-devel gd gd-devel

# Install apache
RUN yum install -y httpd mod_ssl
# RUN openssl genrsa > server.key
# RUN yes | openssl req -new -key server.key > server.csr
# RUN openssl x509 -req -signkey server.key < server.csr > server.crt
# RUN rm server.csr
RUN mkdir /etc/httpd/conf/ssl.key
RUN mkdir /etc/httpd/conf/ssl.crt
COPY server.key /etc/httpd/conf/ssl.key/server.key
COPY server.crt /etc/httpd/conf/ssl.crt/server.crt
COPY ssl.conf /etc/httpd/conf.d/ssl.conf

# Install nginx from source
WORKDIR /var/tmp
RUN wget http://nginx.org/download/nginx-1.9.2.tar.gz
RUN tar -xzvf nginx-1.9.2.tar.gz
RUN git clone https://github.com/chobits/ngx_http_proxy_connect_module.git
WORKDIR /var/tmp/nginx-1.9.2
RUN patch -p1 < /var/tmp/ngx_http_proxy_connect_module/patch/proxy_connect.patch
RUN ./configure --add-module=/var/tmp/ngx_http_proxy_connect_module
RUN make && make install
RUN alias nginx=/usr/local/nginx/sbin/nginx
COPY nginx.conf /usr/local/nginx/conf/nginx.conf
COPY nginx.service /usr/lib/systemd/system/nginx.service

RUN yum -y install dnsmasq
COPY hosts /etc/hosts

# systemctl start dnsmasq

# Install PHP
RUN yum -y install --enablerepo=remi,remi-php80 php php-devel php-mbstring php-pdo php-xml php-gd php-fpm php-mysqlnd php-opcache php-pecl-zip libzip5

# appach run
RUN systemctl enable httpd
RUN systemctl enable dnsmasq
RUN systemctl enable nginx

COPY vhost.conf /etc/httpd/conf.d/vhost.conf

CMD ["/sbin/init"]
