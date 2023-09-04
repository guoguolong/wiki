以如下配置代码为例讲解：

```nginx
server {
    listen      80;
    server_name  web.kaulware.me;
    root /var/html/www/;
    location ~ ^/study {
        index index.php index.html index.htm;
        root /projects/;
        rewrite ^/study/en /study/jp.php;
        rewrite ^/study/jp /study/fr.php last;
        rewrite ^/study/fr /study/us.php;

        location ~ \.php$ {
            fastcgi_index /index.php;
            fastcgi_pass 127.0.0.1:9000;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
        }
    }
    location ~ \.php$ {
        fastcgi_index /index.php;
        fastcgi_pass 127.0.0.1:9000;
        include fastcgi_params;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
    rewrite ^/study/en /simple.html;
}
```

## 1. server块里的location和rewrite，rewrite优先！

上面server块中有3个规则：

1. rewrite ^/study/en /simple.html;
2. location ~ ^/study
3. location ~ .php$ 访问 http://web.kaulware.me/study/en.php ，显然3个规则都能匹配，所以rewrite优先，即访问/var/html/www/simple.html

顺便，如果没有rewrite，那么location哪个在前，就哪个生效(即使后面的声明更匹配)！

## 2. location块里的location和rewrite，location优先！

访问 http://web.kaulware.me/study/fr.php ，虽然rewrite ^/study/fr /study/us.php; 匹配，但是 location ~ .php$也是匹配的，所以后者生效

## 3. rewrite规则本身机制

rewrite默认格式为：

```nginx
rewrite [匹配规则regexp]  [重写目标replacement] [flag标记];
```

flag标记有4个：last、permanent、redirect、break，还可以不写“flag标记”。

### flag标记说明：

#### * 不写[flag标记]，继续匹配后面的规则（当然，只有规则重叠，匹配后面的规则才有影响）

比如定义：

```nginx
 location ~ ^/study {
        ... 
        rewrite ^/study/jp /any;
        rewrite ^/any /study/us.php; # 最后一条规则
       location ~ \.php$ {
       ....
}
```

访问 http://web.kaulware.me/study/jp，匹配 rewrite ^/study/jp /any; 然后继续匹配 rewrite ^/any /study/us.php; 最后一个匹配重叠的rewrite规则重新发起调用，location ~ ^/study中location ~ .php$规则生效.

！！！注意rewrite ^/study/jp /any; 不是最后一个规则，匹配后没有重新发起调用！！！

#### * last #本条规则匹配完成后，继续向下匹配新的location URI规则

比如上面例子定义了

```
rewrite ^/study/en /study/jp.php;
rewrite ^/study/jp /study/fr.php last;
rewrite ^/study/fr /study/us.php;
```

访问 http://web.kaulware.me/study/jp，就会命中rewrite ^/study/jp /study/fr.php last; 然后重新发起了http://web.kaulware.me/study/fr.php调用， 使location ~ ^/study中location ~ .php$规则生效.

#### * redirect #返回302临时重定向，浏览器地址会显示跳转后的URL地址

和last共同点：不继续执行后面的rewrite规则

#### * permanent #返回301永久重定向，浏览器地址栏会显示跳转后的URL地址

和last共同点：不继续执行后面的rewrite规则

#### * break #本条规则匹配完成即终止，不再匹配后面的任何规则

根本不会重新发起调用，直接在root下寻找replacement

！！！总结记忆！！！ rewrite规则匹配后，不外乎3种选择： I. 继续规则执行（尝试匹配后面的rewrite，直到最后一个匹配再发起重新HTTP调用）：不写flag标记 II. 停止规则执行：break标记 III. 停止规则执行，并重新发起HTTP调用：last、redirect、permanent标记