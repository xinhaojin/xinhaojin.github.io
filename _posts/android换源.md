---
title: Android Stdio换源（阿里云镜像源）
tags: []
id: '317'
categories:
  - - 安卓
date: 2020-04-14 15:31:30
---

最近在学安卓开发，好不容易兴致上来要开始写了，结果项目build一直转圈…真是搞得兴致全无。

说白了就是下载依赖资源包巨慢，经常连接超时。我寻思着我也没被墙啊，真是不太明白…

好在万能的阿里云有镜像源，可配置阿里云镜像，加速下载。

特此记录。

配置方法：

1\. 对特定项目生效，在项目中的build.gradle修改内容
```java
buildscript {
    repositories {
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/google' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/gradle-plugin' }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.4.1'
        
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
```

2\. 对所有项目生效,在用户/.gradle/下创建init.gradle文件，内容如下:
```java
allprojects{
    repositories {
        def ALIYUN\_REPOSITORY\_URL = 'http://maven.aliyun.com/nexus/content/groups/public'
        def ALIYUN\_JCENTER\_URL = 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN\_REPOSITORY\_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN\_JCENTER\_URL."
                    remove repo
                }
            }
        }
        maven {
                url ALIYUN\_REPOSITORY\_URL
            url ALIYUN\_JCENTER\_URL
        }
    }
}
```
Done.