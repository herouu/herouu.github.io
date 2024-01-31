### jdk源

<https://www.injdk.cn/>

### native

```xml
            <plugin>
                <groupId>org.graalvm.buildtools</groupId>
                <artifactId>native-maven-plugin</artifactId>
                <version>0.9.28</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                    </execution>
                </executions>
                <configuration>
                    <imageName>${project.name}</imageName>   
                    <mainClass>io.vertx.core.Launcher</mainClass>
                    <buildArgs>-H:+PrintClassInitialization -H:+ReportExceptionStackTraces</buildArgs> 
                </configuration>
            </plugin>

```

* 反射配置辅助生成

```bash
java -agentlib:native-image-agent=config-output-dir=reflection-config.json -jar example.jar

```

* native可执行文件压缩

```bash
upx -o 新文件名 原始文件名
```
