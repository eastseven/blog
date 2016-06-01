title: Spring Boot DevTools 使用小记
date: 2016-06-01 11:36:31
tags:
 - spring-boot
 - spring-boot-devtools
---


#### 1. 环境
* spring boot 1.3.5


#### 2. pom.xml [官方教程](http://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-devtools.html)
添加如下代码：

```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

照着抄即可，但是使用中会遇到一个问题，模板引擎如Freemarker，Thymeleaf等即使在application.properties里设置了cache为false，也不能像之前1.2.X版本时那样，实时修改页面代码。

解决办法：

```
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
        <addResources>true</addResources>
      </configuration>
    </plugin>
  </plugins>
</build>
```

用来自从1.3.X后，src/main/resource没有在devtools的restart监听下了，[传送门](https://github.com/spring-projects/spring-boot/wiki/spring-boot-1.3-release-notes)

> Maven plugin

> spring-boot:run resources

> The Spring Boot Maven plugin no longer adds src/main/resources directly to the classpath when using spring-boot:run. If you want live, in-place editing we recommend using Devtools. The addResources property can be set in your pom.xml if you want to restore Spring Boot 1.2. behavior.
