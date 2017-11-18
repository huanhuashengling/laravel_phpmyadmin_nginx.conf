# laravel_phpmyadmin_nginx.conf
nginx.conf use for pattern like this: http://ip(domain)/phpmyadmin as phpmyadmin index page and http://ip(domain)/ as laravel root


Assume you have setup all the develop or production environment;

Like centos + nginx + mysql + php56 + php-fpm + git + composer;

Download the phpmyadmin and unpack into www root;

Clone your laravel project code into your www root.

First make syslink to nginx html root
then use these code to make things working.

```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

   # access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  server_name;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
	root /your/laravel/project/public;
	index index.php;
        location / {
        	try_files $uri $uri/ /index.php?query_string;    
        }
        
	location ~* \.(jpg|jpeg|gif|css|png|js|ioc|html)$ {
		access_log off;
		expires max;
	}

    	location /phpmyadmin {
		root /.../local/share/nginx/html/;
		index index.php;
		location ~ ^/phpmyadmin/(.+\.php)$ {
	    		try_files $uri =404;
	    		root /.../local/share/nginx/html/;
	    		fastcgi_pass 127.0.0.1:9000;
	    		fastcgi_index index.php;
	    		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	    		include fastcgi.conf;
  		}
		location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ { 
			alias /.../local/share/nginx/html/; 
		}
	}
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   share/nginx/html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            	fastcgi_pass   127.0.0.1:9000;
            	fastcgi_index  index.php;
	        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
            	include        fastcgi.conf;
        }
		
        location ~ /\.ht {
            deny  all;
        }
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   share/nginx/html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   share/nginx/html;
    #        index  index.html index.htm;
#	include vhost/*.conf; 
}

```
