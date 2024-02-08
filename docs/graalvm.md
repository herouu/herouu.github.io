### jdk源

<https://www.injdk.cn/>

### 项目中使用mvnw

```bash
mvn -N wrapper:wrapper
```

### native-image配置

<https://www.graalvm.org/latest/reference-manual/native-image/overview/Options/>

### docker构建

<https://www.graalvm.org/latest/reference-manual/native-image/guides/containerise-native-executable-and-run-in-docker-container/>

### 多阶段native构建

#### glibc

* Dockerfile_glibc

```docker
FROM ghcr.io/graalvm/native-image-community:21 AS builder
# Set the working directory to /home/app
WORKDIR /build

# Copy the source code into the image for building
COPY . /build

# Build
RUN ./mvnw native:compile-no-fork -Pnative

# The deployment Image
FROM jeanblanchard/alpine-glibc:3.19.0

EXPOSE 8080

# Copy the native executable into the containers
COPY --from=builder /build/target/sb-native-test app
ENTRYPOINT ["/app"]
```

```bash
docker build --cpuset-cpus=4 -f Dockerfile_glibc -t sb-native-test:1.0.0-glibc .
```

#### musl

* Dockerfile_musl

```docker
FROM ghcr.io/graalvm/native-image-community:21-muslib AS builder
# Set the working directory to /home/app
WORKDIR /build

# Copy the source code into the image for building
COPY . /build

# Build
RUN ./mvnw native:compile-no-fork -Pnative

# The deployment Image
FROM alpine:3.19.0

EXPOSE 8080

# Copy the native executable into the containers
COPY --from=builder /build/target/sb-native-test app
ENTRYPOINT ["/app"]
```

```bash
docker build --cpuset-cpus=4 -f Dockerfile_musl -t sb-native-test:1.0.0-musl .
```

* 构建时内存、cpu限制

<https://www.graalvm.org/latest/reference-manual/native-image/overview/BuildConfiguration/>

### 基础镜像

* bellsoft/alpaquita-linux-base:stream-glibc
* jeanblanchard/alpine-glibc:3.19.0
* alpine:3.19.0

### small image

<https://javalin.io/2018/09/27/javalin-graalvm-example.html>
<https://www.jetdrone.xyz/2018/08/10/Vertx-native-image-10mb.html>

### springboot xml

```xml

 <plugin>
                <groupId>org.graalvm.buildtools</groupId>
                <artifactId>native-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile-no-fork</goal>
                        </goals>
                        <phase>compile</phase>
                    </execution>
                </executions>
                <configuration>
                    <buildArgs combine.children="append">
                        <buildArg>--parallelism=4</buildArg>
                        <buildArg>--static</buildArg>
                        <!-- glibc, musl, bionic可选 -->
                        <buildArg>--libc=musl</buildArg>
                        <buildArg>--enable-url-protocols=http</buildArg>
                        <buildArg>--features=io.github.herouu.nativetest.nativebuild.LambdaRegistrationFeature
                        </buildArg>
                    </buildArgs>
                    <fallback>false</fallback>
                    <skipNativeTests>true</skipNativeTests>
                </configuration>

            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <id>process-aot</id>
                        <goals>
                            <goal>process-aot</goal>
                        </goals>
                        <phase>compile</phase>
                    </execution>
                </executions>
            </plugin>
```
