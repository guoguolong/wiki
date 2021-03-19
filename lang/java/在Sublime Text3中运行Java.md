## 安装PackageResouceViewer插件

执行Extract Package中的Java，到Packages/java目录下修改 JavaC.sublime-build文件, 内容如下：

```yaml
{
    "cmd": ["javac", "$file"],
    "file_regex": "^(...*?):([0-9]*):?([0-9]*)",
    "selector": "source.java",
    "working_dir": "${project_path:${file_path}}",
    "env": {"CLASSPATH": ".:/opensource/crash.course.java/mars-mvn/src/main/java:/Users/AllenGuo/.m2/repository/org/apache/commons/commons-csv/1.7/commons-csv-1.7.jar"},
    "variants": [

        { 
            "name": "Run",
            "cmd": "java -cp .:${project_path}/src/main/java:/Users/AllenGuo/.m2/repository/org/apache/commons/commons-csv/1.7/commons-csv-1.7.jar com.banyuan.mars.${file_base_name}",
            "shell": true,
            "working_dir": "${project_path}/src/main/resources"
        }
    ]
}
```

注意：存储当前目录为一个项目，有一个 *.sublime-project的文件，否则 ${project_path}为空。