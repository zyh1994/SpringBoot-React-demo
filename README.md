# SpringBootReactDemo
Spring Boot With React JS APP as react Sample Project

SpringBoot与react集成样例

## Project Structure
* src/main/reactdemo: React JS Application initialized by [create-react-app](https://github.com/facebook/create-react-app)
* others: Spring Boot Application initialized by [spring initializer](https://start.spring.io/)

## Requirements
* [NPM](https://www.npmjs.com/) - for Javascript management 安装node.js时会内置安装NPM
* [Gradle](https://gradle.org) 

## Development
The following configuration is added to package.json
```
"proxy": "http://localhost:8080"，springboot中项目默认端口为8080，如果springboot项目端口更改
请同时修改react项目中package.json文件
```
So when you use ***npm start*** to run and develop ReactJS App, all request that can't be recognized by ReactJS App(Port 3000) will be redirected to SpringBoot Application(Port 8080)

## Package
### 1、通过gradle创建SpringBoot项目
```
build.gradle 内容如下：
    buildscript {
        ext {//版本
            springBootVersion = '2.1.0.RELEASE'
        }
        repositories {
        //maven库
            maven {//阿里库
        		url "http://maven.aliyun.com/nexus/content/groups/public/"
        	}
            jcenter()
        }
        //gradle启动springboot基本插件
        dependencies {
            classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
        }
    }
    //java插件
    apply plugin: 'java'
    //springboot插件
    apply plugin: 'org.springframework.boot'
    apply plugin: 'io.spring.dependency-management'
    //命名
    group = 'com.dream.demo'
    version = '0.0.1'
    sourceCompatibility = 1.8
    //jar包下载，区别：上述仓库用于下载启动插件，这里用于下载运行jar包
    repositories {
        maven {//阿里库
            		url "http://maven.aliyun.com/nexus/content/groups/public/"
            	}
        mavenLocal()
        jcenter()
    }
    dependencies {
        implementation('org.springframework.boot:spring-boot-starter-web')
        testImplementation('org.springframework.boot:spring-boot-starter-test')
    }
setting.gradle 内容如下：
    rootProject.name = 'spring-boot-react'
```
### 2、创建react项目
```
笔者采用idea 的terminal 功能。
1、进入src/main目录 cd src/main
2、npx create-react-app reactdemo 等待下载，其中reactdemo为项目名称
3、修改package.json文件，在最后增加"proxy": "http://localhost:8080"
```
### 3、修改build.gradle内容,增加gradle.properties文件
```
1、增加build task任务：
    task npmBuild(type: Exec) {
        //设置为reactdemo的相对路径./src/main/reactdemo
        workingDir NODE_PROJ_DIR
        //通过cmd运行，npm run build
        commandLine 'cmd', '/c', NODE_MANAGER+' run build'
    }
2、增加install任务
    task npmInstall(type: Exec) {
    workingDir = file(NODE_PROJ_DIR)
    commandLine 'cmd', '/c', NODE_MANAGER+' install'
}
3、增加编译复制任务，该任务依赖build任务
    task frontendAssemble(type: Copy, dependsOn: "npmBuild") {
        //将react项目的build下所有文件拷贝至springboot的static中
        def buildDir = file("$NODE_PROJ_DIR/build")
        from buildDir
        into SPRING_STATIC
    }
4、gradle.properties文件内容如下：
    NODE_PROJ_DIR=./src/main/test
    NODE_MANAGER=npm
    SPRING_STATIC=build/resources/main/static

```