---
layout: post
title:  "Android ButterKnife 完全使用手册"
date:   2016-08-19 09:36:25
categories: Android 开源 Library
---


### ButterKnife——Android开发中必用的库

#### 一、简介
  ButterKnife是现在很多Android应用开发者都会使用的开源库，它可以将你从冗余、模板式的`findViewById(int resId)`、`setOnClickListener`、`setOnItemClickLister`等代码中解放出来，极大地提高编程效率。不同于其他依赖注入的框架，它只针对Android的View的字段和方法进行绑定。

  ![ButterKnife图标][ButterKnife图标]

#### 二、使用配置
  在工程级的`build.gradle`中引入`android-apt`插件:
  ```
  buildscript {
  repositories {
    mavenCentral()
   }
  dependencies {
    classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
  }
}
  ```
  在模块级的`build.gradle`中应用`android-apt`插件并添加ButterKnife的依赖:
  ```
  apply plugin: 'android-apt'

android {
  ...
}

dependencies {
  compile 'com.jakewharton:butterknife:8.2.1'
  apt 'com.jakewharton:butterknife-compiler:8.2.1'
}
  ```
  注意：文章中`butterknife`、`butterknife-compiler`以及`android-apt`的版本是当时的最新版本，请在[The Central Repository][mavenCentral]上查找最新的版本。

#### 三、Show me the code



[ButterKnife图标]: http://jakewharton.github.io/butterknife/static/logo.png
[mavenCentral]: http://search.maven.org/#search%7Cga%7C1%7C
