---
layout: post
title: "Spring 版本冲突"
date: 2015-8-11 19:00:24 +0800
comments: true
categories: Java
---

NoSuchMethodError: org.springframework.beans.factory.xml.BeanDefinitionParserDelegate.parseBeanDefinitionAttributes

<!-- more -->

{% img /images/spring/1.jpeg %}

首先怀疑是spring 类冲突，操作步骤如下：

1. 在项目的target目录（也可以把war解压缩，在解压后的目录下）下查找spring的jar

    ``` bash
     ​find . -name "*spring*"
    ```

    结果如下，发现有一2.0.7版本的spring，和别的3.0.5的都不一样

    {% img /images/spring/2.jpeg %}

2. 使用mvn dependency:tree找到具体哪个dependency引用的这个版本的spring

3. 使用exclusions在pom.xml中排除对spring的引用

    ``` xml pom.xml
    <dependency>
        <groupId>com*********</groupId>
        <artifactId>*****</artifactId>
        <version>1.2.5</version>
        <exclusions>
            <exclusion>
                <groupId>org.springframework</groupId>
                <artifactId>spring</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aspects</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    ```
4. 重新生成部署，问题消失。

问题原因

​找到spring-beans-3.0.5.RELEASE.jar和spring-2.0.7.jar.用反编译工具分别找到BeanDefinitionParserDelegate的定义。发现在3.0.5版本中有parseBeanDefinitionAttributes方法而2.0.7中没有。

{% img /images/spring/3.jpeg '3.0.5' %}

{% img /images/spring/4.jpeg '2.0.7' %}

其它有用工具：

pmap <进程号>  找出Java进程加载的jar 包。
