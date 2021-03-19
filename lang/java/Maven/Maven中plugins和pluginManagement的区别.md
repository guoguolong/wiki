pluginManagement是表示插件声明，即你在项目中的pluginManagement下声明了插件，Maven不会加载该插件，pluginManagement声明可以被继承。

pluginManagement一般是用来在父POM中定义，提供给子POM使用，子POM也可以覆盖这个定义，而且你在父POM中定义了版本之后，子模块中直接应用groupId和artifactId，而不用指定版本，同时也方便统一管理；而在父POM中的pluginManagement并不会介入到Maven的生命周期。

plugins就是直接引入一个plugin，而且可以绑定到Maven相关的生命周期上。

pluginManagement主要是为了统一管理插件，确保所有子POM使用的插件版本保持一致，类似dependencies和dependencyManagement。

## 例

### 父POM

```xml
<pluginManagement>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
            <version>2.1</version>
            <configuration>
                <attach>true</attach>
            </configuration>
            <executions>
                <execution>
                    <phase>compile</phase>
                    <goals>
                        <goal>jar</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</pluginManagement>
```

### 子POM

```xml
<plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
    </plugin>
</plugins>
```