http://maven.apache.org/guides/getting-started/maven-in-five-minutes.html

## I. 学习路径

### 入门文档

进入官网，点击左侧菜单Use页，文档学习顺序

[The 5 minute test] -> [Getting Started Tutorial] -> [Reference: POM、Setting]

### 学习plguins

Maven的一切皆是Plguins，所以学习Plugins就是学习Maven，Maven官方到底有哪些插件？

进入官网，点击左侧菜单[Maven Plugins]页，页面列出了Plugins代码地址和文档，也有存放官方插件的中央仓库URL，链接为：https://repo.maven.apache.org/maven2/org/apache/maven/plugins/

### 了解dependencies

Maven中央仓库现在的存放的依赖包到底有多庞大？

进入官网，点击左侧菜单[Maven Central Repositoy]->[Central Index]，页面提供了一个链接 https://repo.maven.apache.org/maven2/.index/ ，可以查看依赖包的列表。不过这个这个方式很糙，更一把的方法，是去一些专门提供友好Web界面查询Maven仓库的网站进行查询，如： https://mvnrepository.com

## II. 实战

### 创建项目

```
mvn archetype:generate -DgroupId=com.banyuan.mars -DartifactId=mars -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

## MAVEN生命周期

### clean声明周期：(3 phase(阶段))

- pre-clean
- clean:清理上一次构建生成的文件
- post-clean

### default声明周期：（23 phase）

- validate
- initialize
- generate-sources
- process-sources:处理项目主资源文件，拷贝src/main/resources 到classpath
- generate-resources
- process-resources
- compile:编译src/main/java代码输出到classpath
- process-classes
- generate-test-sources
- process-test-sources:对src/test/resource目录进行变量替换，拷贝到test的classpath
- generate-test-resources
- process-test-resources
- test-compile
- process-test-classes
- test
- prepare-package
- package
- pre-integration-test
- integeration-test
- post-integration-test
- verify
- install:安装到本地仓库
- deploy

## site生命周期：(4 phase)

- pre-site
- site
- post-site
- site-deploy