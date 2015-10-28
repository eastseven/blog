title: Maven 小抄
date: 2015-08-20 08:09:52
tags:
 - maven
categories: 小抄
---

### 1.创建java工程：

    mvn archetype:generate \
    -DinteractiveMode=false \
    -DarchetypeCatalog=internal \
    -DarchetypeGroupId=org.apache.maven.archetypes \
    -DgroupId=cn.eastseven \
    -DartifactId=demo-xxx
    
### 2.创建web工程：

    mvn -B archetype:generate \
    -DinteractiveMode=false \
    -DarchetypeCatalog=internal \
    -DarchetypeArtifactId=maven-archetype-webapp \
    -DgroupId=cn.eastseven \
    -Dpackage=cn.eastseven.demo.web \
    -Dversion=0.0.1 \
    -DartifactId=demo-xxx-xx

### 3.web插件

    <!-- Tomcat6 -->
    <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat6-maven-plugin</artifactId>
        <version>2.2</version>
    </plugin>
    <!-- Tomcat7 -->
    <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
        <configuration>
            <path>/web</path>
            <port>9527</port>
        </configuration>
    </plugin>
    <!-- Jetty -->
    <plugin>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-maven-plugin</artifactId>
        <version>9.2.8.v20150217</version>
    </plugin>