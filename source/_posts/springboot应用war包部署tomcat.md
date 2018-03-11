---
title: springboot应用war包部署tomcat
date: 2018-03-01 23:46:57
tags: springboot
---

又要使用springboot，又得用tomcat部署。
还不如只用传统的。
springboot默认打包成jar包。
```
mvn package
```
```
[INFO] --- maven-jar-plugin:2.6:jar (default-jar) @ xpay ---
[INFO] Building jar: /media/abel/Dev/java_src/springbootdemo/target/springbootdemo-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:1.5.9.RELEASE:repackage (default) @ xpay ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 4.679 s
[INFO] Finished at: 2018-03-01T23:27:10+08:00
[INFO] Final Memory: 28M/343M
[INFO] ------------------------------------------------------------------------
```
<!-- more -->

开始尝试直接打包成war包，发现路径什么的不对，在tomcat无法运行。
springboot使用外部tomcat需要如下：

1.更改pom.xml打包成war。
```
    <groupId>cn.jiangshaobo</groupId>
    <artifactId>springbootdemo</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
```

2、添加maven的war打包插件如下：并且给war包起一个名字，tomcat部署后的访问路径会需要，如：http:localhost:8080/myweb/****

```
<plugin>       
   <groupId>org.apache.maven.plugins</groupId>       
   <artifactId>maven-war-plugin</artifactId>       
   <configuration>       
    <warSourceExcludes>src/main/resources/**</warSourceExcludes>  
    <warName>myweb</warName>       
   </configuration>       
  </plugin>
```

3、排除org.springframework.boot依赖中的tomcat内置容器，这是很重要的一步
```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

4、添加对servlet API的依赖
```
        <dependency>  
            <groupId>javax.servlet</groupId>  
            <artifactId>javax.servlet-api</artifactId>  
        </dependency>
```