去官网：https://www.elastic.co 参考最合适，以下基本是官网摘录.

# I. ElasticSearch 6.2.1

https://www.elastic.co/cn/downloads/elasticsearch

### 1. 下载tar包解压缩

### 2. 运行

```bash
 bin/elasticsearch
```

### 3.测试

```
http://localhost:9200
```

### 4. Web管理插件

https://github.com/mobz/elasticsearch-head

```
git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start
open http://localhost:9100/
```

# II. LogStash 6.2.1

https://www.elastic.co/cn/downloads/logstash

### 1. 下载tar包解压缩

### 2. 准备配置文件(解析默认的nginx格式)

```
#logstash.conf
input { 
  file {
      path => ["/var/log/nginx/access*.log"]
      start_position => beginning 
  }
}

filter {  
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG} %{QS:x_forwarded_for}"}
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index  => "nginx-log"
  }
  stdout { 
    codec => rubydebug
  }
}
```

### 3. 运行

```bash
bin/logstash -f logstash.conf
```

# III. Kibana 6.2.1

https://www.elastic.co/cn/downloads/kibana

### 1. 下载tar包解压缩

### 2. 设置配置文件

打开config/kibana.yml，设置elasticsearch.url指向Elasticsearch实例.

### 3. 运行

```bash
bin/kibana 
```

### 4. 浏览查看

```
http://localhost:5601
```