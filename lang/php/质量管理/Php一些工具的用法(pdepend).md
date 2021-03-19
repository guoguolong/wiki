## pdepend (https://pdepend.org/)

pdepend能度量软件的复杂度，比如：通过分析的包被调用其它包或被调用的次数给出复杂度图表. 使用composer安装pdepend.

> composer global require pdepend/pdepend:2.2.4

产生相关命令文件pdepend, 执行如下命令产生报表

```
pdepend --summary-xml=/tmp/summary.xml \
               --jdepend-chart=/tmp/jdepend.svg \
               --overview-pyramid=/tmp/pyramid.svg \
               /src
```

报表的确有点抽象，可参考 https://pdepend.org/documentation/getting-started.html 学习。