### mirror设置

```xml
<mirrors>
  <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>https://maven.aliyun.com/repository/public</url>
      <mirrorOf>central</mirrorOf>        
  </mirror>
</mirrors>  
```

* 获取依赖源码及文档

```bash
mvn dependency:sources -DdownloadSources=true -DdownloadJavadocs=true
```

### 发布jar包

说明：个人使用的场景推荐gitea的Package Registry功能，企业级场景还是要使用nexus搭建私服

* settings.xml

```xml
<servers>
    <server>
      <id>gitea</id>
      <configuration>
        <httpHeaders>
          <property>
            <name>Authorization</name>
            <value>token {access_token}</value>
          </property>
        </httpHeaders>
      </configuration>
    </server>
  </servers>

```

#### 两种方式

* 方式一 项目中添加pom.xml

```xml

    <repositories>
        <repository>
            <id>gitea</id>
            <url>https://home.herouu.top/git/api/packages/herouu/maven</url>
        </repository>
    </repositories>
    <distributionManagement>
        <repository>
            <id>gitea</id>
            <url>https://home.herouu.top/git/api/packages/herouu/maven</url>
        </repository>
        <snapshotRepository>
            <id>gitea</id>
            <url>https://home.herouu.top/git/api/packages/herouu/maven</url>
        </snapshotRepository>
    </distributionManagement>
```

* 方式二 settings.xml添加(推荐)

```xml

<profiles>
    <profile>
      <id>default</id> 
      <repositories>
        <repository>
          <id>aliyun-repo</id> 
          <url>https://maven.aliyun.com/repository/public</url> 
          <releases>
            <enabled>true</enabled>
          </releases> 
          <snapshots>
            <enabled>true</enabled> 
            <updatePolicy>always</updatePolicy>
          </snapshots>
        </repository>
      </repositories>
    </profile> 
    <profile>
      <id>gitea</id> 
      <properties>
        <!-- 此处的gitea要与server.id对应,即配置认证 -->
        <altSnapshotDeploymentRepository>gitea::default::https://home.herouu.top/git/api/packages/herouu/maven/snapshots</altSnapshotDeploymentRepository>
        <altReleaseDeploymentRepository>gitea::default::https://home.herouu.top/git/api/packages/herouu/maven/releases</altReleaseDeploymentRepository>
      </properties>
      <repositories>
        <repository>
          <id>gitea</id> 
          <url>https://home.herouu.top/git/api/packages/herouu/maven</url> 
          <releases>
            <enabled>true</enabled>
          </releases> 
          <snapshots>
            <enabled>true</enabled> 
            <updatePolicy>always</updatePolicy>
          </snapshots>
        </repository>
      </repositories>
    </profile>
<profiles>
<activeProfiles>
  <activeProfile>default</activeProfile>
</activeProfiles> 
```

项目中添加插件，version>=2.8.0

```xml
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-deploy-plugin</artifactId>
                <version>2.8.2</version>
            </plugin>
        </plugins>
    </build>
```

```vim
# 发布jar包至私服需要指定profile

mvn clean deploy -Pgitea
```

### 插件

#### maven-shade-plugin

* maven-shade-plugin主要用于创建一个包含所有依赖项的可执行JAR文件。
* 它会将项目的编译输出与所有依赖项合并到一个JAR文件中，并对冲突的类和资源进行重定向和重命名，以避免命名冲突问题。
* 这个插件通常用于创建独立的可执行JAR文件，可以直接通过java -jar命令运行。

#### maven-assembly-plugin

* maven-assembly-plugin主要用于创建自定义的分发包或归档文件。
* 它可以根据用户定义的描述文件，将项目的源代码、资源文件、配置文件等打包成ZIP、TAR.GZ等格式的归档文件。
* 这个插件通常用于创建发布版本、分发包或者用于部署的压缩文件。

总结：maven-shade-plugin和maven-assembly-plugin在功能上有一些重叠，都可以用于构建可执行的Java应用程序。然而，maven-shade-plugin更关注于创建可执行JAR文件，而maven-assembly-plugin则更适合创建自定义的分发包或归档文件。选择使用哪个插件取决于你的具体需求和项目的特点。
