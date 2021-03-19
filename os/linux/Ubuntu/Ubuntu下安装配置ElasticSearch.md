### 1. 准备依赖环境

依赖jdk, 本例使用openjdk from ppa

```
add-apt-repository ppa:ikuya-fruitsbasket/openjdk
apt-get update
apt-get install openjdk-8-jre
```

### 2. ES 及 head 插件

可从[ES官网](https://www.elastic.co/downloads/elasticsearch)找到deb版本下载链接

```
wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/deb/elasticsearch/2.3.3/elasticsearch-2.3.3.deb
sudo dpkg -i elasticsearch-2.3.3.deb

// 安装 head 插件
cd /usr/share/elasticsearch/
sudo bin/plugin install mobz/elasticsearch-head
```

### 3.访问ElasticSearch服务

```
http://localhost:9200  即可，如果es在远程主机，希望通过http://es.kaulware.com运行，则配置一个nginx代理吧
```

### 4. 中文分词插件ik

es版本对应的ik插件版本见[ik插件github](https://github.com/medcl/elasticsearch-analysis-ik), 此处有安装说明, 不必重新打包, 打包过的 release 可从[RELEASES](https://github.com/medcl/elasticsearch-analysis-ik/releases)下载. 下载之后解压到 plugins/ik 目录.

```
// ik-1.9.3 用于es2.3.3 
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v1.9.3/elasticsearch-analysis-ik-1.9.3.zip
// 复制然后解压缩 elasticsearch-analysis-ik-{version}.zip 到 your-es-root/plugins/ik
```

然后需要配置中文分词插件.

```
index.analysis.analyzer.ik.type: "ik"
index.analysis.analyzer.default.analyzer: "ik_max_word"
index.analysis.analyzer.default.tokenizer: "ik_max_word"
```

注: ik_max_word: 例"中华人民共和国国歌"拆分为"中华人民共和国","中华人民","共和国","国歌","华人","共和","国" ik_smart: 例"中华人民共和国国歌"拆分为"中华人民共和国","国歌"

### 5. 确认head及分词插件

http://es.babyun.me/_plugin/head/ 选索引, 动作->测试分词器, 输入中文词组,分析有CN_WORD即为预期结果.

\##配置中文搜索踩过的坑 从[elasticsearchRTF](https://github.com/medcl/elasticsearch-rtf)拷过来配置好的中文分词, 去掉冗余之后,贴在 /etc/elasticsearch/elasticsearch.yml, 创建索引时出错: [failed to create index]; nested: IllegalArgumentException[Unknown Analyzer type [ik_max_word] for [default]];

什么是ElasticSearch-RTF？ RTF是Ready To Fly的缩写，在航模里面，表示无需自己组装零件即可直接上手即飞的航空模型，elasticsearch-RTF是针对中文的一个发行版，即使用最新稳定的elasticsearch版本，并且帮你下载测试好对应的插件，如中文分词插件等，还会帮你做好一些默认的配置，目的是让你可以下载下来就可以直接的使用. 从 RTF 移植过来的配置

```
index:
  analysis:
    analyzer:
      ik:
        alias:
        - ik_analyzer
        type: ik
      ik_max_word:
        type: ik
        use_smart: false
      ik_smart:
        type: ik
        use_smart: true

#index.analysis.analyzer.default.type: mmseg
index.analysis.analyzer.default.type: ik_max_word
```

另外也单独下载过 RTF 来试图用配置好的拼音等, 但是并没有像预期那样工作. head插件也有些问题.