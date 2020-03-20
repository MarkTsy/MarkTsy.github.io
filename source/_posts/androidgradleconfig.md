---
title: AS Gradle常用配置
date: 2020-03-19 08:49:31
tags: [gradle]
categories: Android
---



整理一些常用的Android Studio中gradle的配置

<!--more-->

#### 多个Model统一编译环境

原理很简单,将配置放在一个所有model都可以引用的地方, 在Project的build.gradle添加

```
ext {    // global variables definition    compileSdkVersion = 23    buildToolsVersion = "23.0.1"    minSdkVersion = 17    targetSdkVersion = 19    supportLibVersion = '25.0.1'}allprojects {    repositories {        jcenter()        mavenCentral()        flatDir {            dirs 'libs'        }    }    tasks.withType(JavaCompile) {        options.encoding = "UTF-8"  //统一编码格式    }    tasks.withType(JavaCompile) {        sourceCompatibility = JavaVersion.VERSION_1_7  //统一sdk版本        targetCompatibility = JavaVersion.VERSION_1_7    }}
```

#### Model中引用

```
compileSdkVersion rootProject.ext.compileSdkVersionbuildToolsVersion rootProject.ext.buildToolsVersion...minSdkVersion rootProject.ext.minSdkVersion targetSdkVersion rootProject.ext.targetSdkVersion...dependencies {    compile fileTree(dir: 'libs', include: ['*.jar'])    compile "com.android.support:support-annotations:${rootProject.ext.supportLibVersion}"    compile "com.android.support:support-v4:${rootProject.ext.supportLibVersion}"}
```

#### 文件制定model的versionName/versionCode

model的build.gradle同文件夹下创建文件, 命名为version.properties, 内容:

```
versionName = 1.0versionCode = 1model的build.gradle中解析version.propertiesandroid {    ...    ext.versionFile = file('version.properties')    //动态加载配置文件version.properties里面的属性值    if (!versionFile.exists()) {        throw new GradleException("Required version file does not exist:versionFile.canonicalPath")    }    Properties versionProps = new Properties()    versionFile.withInputStream {        stream -> versionProps.load(stream)    }    def code = versionProps.versionCode.toInteger()    def name = versionProps.versionName    defaultConfig {        ...        versionCode code        versionName name    }}
```

#### 修改编译后的项目名称, 编译后的名称添加versionName,versionCode, 以及渠道名称

```
Android{    applicationVariants.all { variant ->        variant.outputs.each { output ->            def outputFile = output.outputFile            if (outputFile != null && outputFile.name.endsWith('.apk')) {                if (variant.buildType.name == "release") {                    fileName = "Demo_v${name}_${code}_${variant.productFlavors[0].name}_release_unsign.apk"                } else if (variant.buildType.name == "debug") {                    fileName = "Demo_v${name}_${code}_debug_unsign.apk"                } else {                    fileName = "Demo_v${name}_${code}_${variant.productFlavors[0].name}_test_unsign.apk"                }                output.outputFile = new File(outputFile.parent, fileName)            }        }    }}
```

#### gradle中配置项目中的debug开关

```
  buildTypes {        debug {            // hidden Log            buildConfigField "boolean", "LOG_DEBUG", "true"   //项目中可通过BuildConfig.LOG_DEBUG来调用           ...        }...}
```

#### 自动签名

```
 signingConfigs {        debug {            storeFile file("signature/system.jks")  //model通文件夹下创建文件夹signature, 放置签名文件            storePassword "****"            keyAlias "****"            keyPassword "*****"        }        release {          ...        }    }buildTypes {    debug {        ...        signingConfig signingConfigs.release  //应用签名的配置    }
```

流程 : 配置一个签名的配置—> 在buildTypes中使用签名配置, 签名文件可以在签名打包的时候创建, 如果你的签名文件不是jks, 百度一下转换方法即可
