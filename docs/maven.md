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
