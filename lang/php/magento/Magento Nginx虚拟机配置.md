```nginx
server {

  listen 80;

  server_name www.magento2.local magento2.local; # 域名
  root /projects/magento2; # 站点根目录
  index index.php;
  error_log logs/magento2.error.log;
  access_log logs/magento2.access.log;
  location /setup {
    try_files $uri $uri/ @setuphandler;
  }
  # Rewrite Setup's Internal Requests
  location @setuphandler {
    rewrite /setup /setup/index.php;
  }
 
  location / {
    index index.php;
    try_files $uri $uri/ @handler; ## If missing pass the URI to Magento's front handler
    expires 30d; ## Assume all files are cachable
  }
  location @handler { ## Magento uses a common front handler
    rewrite / /index.php;
  }
 location /pub/static {
    rewrite ^/pub/static/(.*)$ /pub/static.php?resource=$1? last;
  }
  # 此段为将PHP请求转交给FastCGI服务，PHP-FPM是非常流行的选项。
  location ~ ^/(.*)\.php(/|$) {
    #if (!-e $request_filename) { rewrite / /index.php last; } ## Catch 404s that try_files miss
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_split_path_info ^(.+\.php)(/.*)$;
    include fastcgi_params;
    fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    fastcgi_param  HTTPS              off;
    fastcgi_param MAGE_MODE "developer";
  }
}
```