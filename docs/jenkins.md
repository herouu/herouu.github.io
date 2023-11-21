### 安装

```bash
 docker run -d \
-v /var/jenkins_home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /usr/bin/docker:/usr/bin/docker \
-v /etc/localtime:/etc/localtime \
-p 10340:8080 -p 10341:50000 \
jenkins/jenkins:lts-jdk11
```

### 插件

* Docker Pipeline
* Extended Choice Parameter

### pipeline

```groovy
 
properties([
    parameters([
        string(name:'branchName', defaultValue: 'prod', description: 'git分支名称'),
        extendedChoice(
                        name: 'modules',
                        defaultValue: '',
                        description: '勾选需要构建的模块',
                        type: 'PT_CHECKBOX',
                        visibleItemCount: '15',
                        value: 'demo'
                    ),
    ])
])

def getData() {
    return [
        'kl-gate':[
            'name':'demo',
            'mvn':'./demo/demo',
            'jar':'./demo/demo/target/demo.jar',
            'port': 8080,
            'node':'jenkins-node'
        ]
    ]
}

def buildStages

def getMaven(str) {
    def arr = str.split(',')
    def mavenStr = ''
    for (i in arr) {
        def mvn = getData().get(i).get('mvn')
        mavenStr += mvn + ','
    }
    return mavenStr.substring(0, mavenStr.length() - 1)
}

def prepareBuildStages(modules) {
    def modulesList = modules.split(',')
    def buildParallelMap = [:]
    for (name in modulesList) {
        def n = "${name}"
        buildParallelMap.put(n, prepareOneBuildStage(n))
    }
    return buildParallelMap
}

def prepareBuildNode(modules) {
    def modulesList = !modules ? getData().keySet() : modules.split(',')
    def bmapList = [:]
    for (name in modulesList) {
        def n = "${name}"
        def list = prepareOneBuildNode(n)
        for (per in list) {
            if (bmapList.containsKey(per)) {
                bmapList.get(per).add(n)
        }else {
                bmapList.put(per, [n])
            }
        }
    }
    return bmapList
}

List prepareOneBuildNode(String name) {
    def xname = getData().get(name).get('node')
    return xname.split(',')
}

def prepareOneBuildStage(String name) {
    def xname = getData().get(name).get('name')
    def xport = getData().get(name).get('port')
    def xpath = getData().get(name).get('jar')
    def xprofile = getData().get(name).get('profile')
    if (!xprofile) {
        xprofile = 'uat'
    }
    return {
        stage('制作镜像') {
            sh "docker build -f ./docker/Dockerfile-Uat --build-arg PROJECT_NAME=${xname} --build-arg path=${xpath} -t ${xname}:1.0.0 ."
            println '----------镜像成功-----------'
        }

        stage('部署') {
            script {
                try {
                    sh "docker rm -f ${xname}"
                } catch (Exception err) {
                }
                sh "docker run  --name ${xname} -m 512m --restart=always -d --net=host --log-opt max-size=100m -v /home/Learning/${xname}/logs:/logs -e JAVA_OPTS='-Dserver.port=${xport}' -e SPRING_PROFILES_ACTIVE=${xprofile} ${xname}:1.0.0"
            // sh '''docker rmi $(docker images | grep 'none' | awk '{print $3}')'''
            }
        }
    }
}


prepareBuildNode("${modules}").each {
        node(it.key) {
            println "节点 ${it.key} 执行modules:${it.value}"
            stage('清空') {
                cleanWs()
            }
            stage('Git阶段') {
                git branch: "${branchName}", url: 'http://github.com/demo.git' ,credentialsId: 'xxx'
                sh "git log --pretty=format:'SHA-1：%h%n提交人：%an%n提交时间：%ad（%ar）%n提交信息：%s' --date=format:'%y-%m-%d %H:%M:%S' --shortstat  --no-merges -n 10"
                println '----------拉取代码成功-----------'
            }

            stage('Maven阶段') {
                def str = getMaven(it.value.join(','))
                docker.image('maven:3.8.6-jdk-8-slim').inside('-v /home/jmsadmin/.m2:/root/.m2') {
                    sh "mvn clean package -f pom.xml  -am -pl ${str} -Dmaven.test.skip=true -Dmaven.compile.fork=true"
                    println '----------编译成功-----------'
                }
            }

            stage('添加docker文件') {
                sh 'rm -rf docker'
                sh 'mkdir docker'
                sh 'touch docker/Dockerfile-Uat'
                sh '''tee docker/Dockerfile-Uat <<-'EOF'
            FROM adoptopenjdk/openjdk8-openj9:alpine-slim

            ARG path
            ARG PROJECT_NAME

            COPY ${path} /usr/share/deploy/app.jar

            ENV TZ=PRC
            ENV ENV_PROJECT_NAME=$PROJECT_NAME
            ENV ENV_PROJECT_NAME=$PROJECT_NAME
            RUN echo $ENV_PROJECT_NAME
            RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
 RUN sed -i 's/jdk\\.tls\\.disabledAlgorithms=SSLv3, TLSv1, TLSv1\\.1/jdk\\.tls\\.disabledAlgorithms=SSLv3/g' /opt/java/openjdk/jre/lib/security/java.security
            ENTRYPOINT java -XX:+PrintGCDateStamps -XX:+PrintGCDetails -Xloggc:/root/logs/$ENV_PROJECT_NAME/gc -XX:+UseContainerSupport -XX:InitialRAMPercentage=50 -XX:MaxRAMPercentage=80 -Djava.security.egd=file:/dev/./urandom $JAVA_OPTS -jar /usr/share/deploy/app.jar
            '''
            }

            stage('Initialise') {
                buildStages = prepareBuildStages(it.value.join(','))
                println('Initialised pipeline.')
            }

            parallel(buildStages)
        }
}

```

### jenkins agent

```bash
 docker run -d --name dev-jenkins --user root -v /var/jenkins_home:/var/jenkins_home -v /usr/bin/docker:/usr/bin/docker -v /var/run/docker.sock:/var/run/docker.sock -v /etc/docker:/etc/docker -v /etc/localtime:/etc/localtime --init jenkins/inbound-agent -url http://192.168.0.1:10000 -workDir=/var/jenkins_home b07fab865535c605ae34d4ec4cf9dfbab791e5995298e927130ef1b8dec3ddd jenkins-node
```
