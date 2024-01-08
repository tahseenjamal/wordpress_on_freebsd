How to setup WordPress on freebsd

# FreeBSD 12.2 x64

How to setup WordPress on freebsd

FreeBSD 12.2 x64
Setting up nginx with vhosts and then mysql8.0, php8.0 with fpm and extensions to run wordpress and owncloud

    pkg install mysql80-server
    sysrc  mysql_enable="YES"
    service mysql-server start

    mysql_secure_installation

#set password, remove anonymous user and remote access
#also when creating user use 'identified with #mysql_native_password by PASSWORD'

    create 'user'@'localhost' identified with mysql_native_password by 'PASSWORD';

#install nginx

    pkg install nginx
    sysrc nginx_enable="YES"
    service nginx start
    cp /usr/local/etc/nginx
    mkdir sites-available sites-enabled
    cp nginx.conf sites-available

#configure nginx conf

    vim nginx.conf

#In http { } just have like below

    http {
    	include			mime.types;
    	default_type	application/octet-stream;
    	sendfile		on;
    	keepalive_timeout  65
    	gzip  on;
    	include /usr/local/etc/nginx/sites-enabled/*;
    }

###Save and quit vim


    cd /usr/local/etc/nginx/sites-available
    touch wordpress
    cd ../sites-enabled
    ln -s ../sites-available/wordpress .
    pkg install -y php80 php80-curl php80-dom php80-posix php80-filter php80-ftp php80-gd php80-iconv php80-mbstring php80-openssl php80-simplexml php80-sockets php80-tokenizer php80-xmlreader php80-zlib php80-mysqli php80-pdo_mysql php80-zip php80-intl

#Now edit php fpm conf

    vim /usr/local/etc/php-fpm.d/www.conf

#Uncomment and check value should as below

    user = www
    group = www
    listen = /var/run/php-fpm.sock
    listen.owner = www
    listen.group = www
    listen.mode = 0660
    php_admin_value[error_log] = /var/log/fpm-php.www.log

## Save and quit vim

    sysrc php_fpm_enable="YES"
    service php-fpm start
    cd /usr/local/www/nginx
    wget https://wordpress.org/latest.zip
    unzip latest.zip
    rm latest.zip
    chown -R www:www wordpress


