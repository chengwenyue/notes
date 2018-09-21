nginx编译选项

	./configure \
	--prefix=/usr/local/nginx\  nginx安装路径 默认/usr/local/nginx 
	--sbin-path=/usr/sbin/nginx\  nginx可执行文件路径 <prefix>/sbin/nginx
	--conf-path=/etc/nginx/nginx.conf\  没有给定-c选项下默认的nginx.conf的路径 默认 <prefix>/conf/nginx.conf
	
	--pid-path=/var/run/nginx/nginx.pid\  在nginx.conf中没有指定pid指令的情况下，默认的pid位置 默认 <prefix>/logs/nginx.pid

	--lock-path=/var/lock/nginx.lock\ nginx.lock文件的路径
	--user=nginx\
	--group=nginx\
	--with-pcre
	--with-http_ssl_module\
	--with-http_flv_module\
	--with-http_gzip_static_module\
	--with-http_stub_status_module \
	--with-http_realip_module\
	--http-client-body-temp-path=/var/tmp/nginx/client/\
	--http-proxy-temp-path=/var/tmp/nginx/proxy/\
	--http-fastcgi-temp-path=/var/tmp/nginx/fcgi/
	
	--error-log-path=/var/log/nginx/error.log\ 默认<prefix>/logs/error.log
	--http-log-path=/var/log/nginx/access.log\  默认<prefix>/logs/access.log
	

	


自定义例子：

	./configure \
	--prefix=/usr/local/nginx \
	--pid-path=/var/run/nginx/nginx.pid \
	--lock-path=/var/lock/nginx.lock \
	--user=nginx \
	--group=nginx \
	--with-http_ssl_module \
	--with-http_flv_module \
	--with-http_gzip_static_module \
	--with-http_stub_status_module \
	--with-http_realip_module


安装命令：

	make && make install