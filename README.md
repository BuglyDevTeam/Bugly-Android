##Bugly（Android） SDK 快速接入向导

---

###一、库文件导入

如果您使用Gradle编译Apk，我们强烈推荐您使用自动接入方式配置库文件（[JCenter仓库](http://jcenter.bintray.com/com/tencent/bugly/crashreport/)）。

####方式1. 自动导入（推荐）

在Module的buid.gradle文件中添加依赖和属性配置：

```groovy
android {
	defaultConfig {
		ndk {
			// 设置支持的SO库架构
			abiFilters 'armeabi' //, 'x86', 'armeabi-v7a', 'x86_64', 'arm64-v8a'
		}
	}
}

dependencies {
	compile 'com.tencent.bugly:crashreport:latest.release' //其中latest.release指代最新版本号，也可以指定明确的版本号，例如1.2.8
}
```

![Alt text](http://bugly.qq.com/img/white-book/af1.png?v=20160203165136)

**后续更新Bugly SDK时，只需变更配置脚本中的版本号即可。**


> `注意：自动集成时会自动包含Bugly SO库，建议在Module的build.gradle文件中使用NDK的“abiFilter”配置，设置支持的SO库架构。`
> 
> 如果在添加**“abiFilter”**之后Android Studio出现以下提示：
> `NDK integration is deprecated in the current plugin.  Consider trying the new experimental plugin.`
> 
> 则在项目根目录的**gradle.properties**文件中添加：
> ```groovy
> android.useDeprecatedNdk=true
> ```

---

####方式2. 手动导入

如果您不采用上述自动导入方式，也可以手动集成Bugly SDK。

**下载Bugly库文件**
- 下载Bugly的[**Android SDK包**](http://bugly.qq.com/whitebook)；
- 如果您的工程有Native代码（C/C++）或者集成了其他第三方SO库，建议下载Bugly的[**NDK动态库**](http://bugly.qq.com/whitebook)。

> Bugly NDK包含多个架构的SO库：
> - armeabi
> - armeabi-v7a
> - arm64-v8a
> - x86
> - x86_64
> 
> 在集成Bugly SO库时，请注意只保留支持的架构SO库。

**Eclipse工程**
- 将Bugly库文件复制到工程的libs目录下；
- Refresh一下工程；
- 添加工程依赖：鼠标右键点击Bugly的JAR文件，添加到编译路径中。
![Alt text](http://bugly.qq.com/img/white-book/af2.png?v=20160203165136)


**Android Studio工程**
- 将Bugly库文件复制到工程的libs目录下；
- 如果集成Bugly NDK，则在Module的buid.gradle文件中添加SO库目录配置：

```groovy
android {
	sourceSets {
        main.jniLibs.srcDirs = ['libs']
    }
}
```

- 点击Sync，同步配置。

![Alt text](http://bugly.qq.com/img/white-book/af3.png?v=20160203165136)

为了使APP Crash堆栈的可读性更高，建议您配置符号表文件，更准确地定位问题：
- 纯Java代码的工程：只需要配置混淆后生成的Mapping文件即可；
- 含有Native代码的工程：建议配置符号表工具从Debug SO中提取的Symbol符号表文件。

Bugly支持手动和自动配置两种方式，具体的配置方法请参考：[《Bugly Android符号表配置》](http://bugly.qq.com/androidsymbol)

---

###二、参数配置

- 在AndroidManifest.xml中添加权限：
```xml
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_LOGS" />
```

- 请避免混淆Bugly，在Proguard混淆文件中增加一行配置：

```powershell
-keep public class com.tencent.bugly.**{*;}
```

---

###三、最简单的初始化

获取APP ID并将以下代码复制到项目Application类onCreate()中，Bugly会为自动检测环境并完成配置：

```java
CrashReport.initCrashReport(getApplicationContext(), "注册时申请的APPID", false);
```
![Alt text](http://bugly.qq.com/img/white-book/af4.png?v=20160203165136)

---

###四、测试

现在您可以制造一个Crash，来体验Bugly的能力了。在初始化Bugly的之后，调用Bugly测Java Crash接口。

```java
CrashReport.testJavaCrash();
```

执行到这段代码时会发生一个Crash，Logcat的TAG=CrashReportInfo中输出为：

![Alt text](http://bugly.qq.com/img/white-book/android5.jpg?v=20160203165136)

现在您已经可以在**“崩溃”**页面看到刚才触发的Crash issue了（延迟一般在10s以内）。
