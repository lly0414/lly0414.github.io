# 记录windows10下安装运行React Native的坑
## 第一个错误
首先根据官方文档的步骤安装jdk,和android sdutio及相关插件都是没有问题的
但是在运行官方示例的时候首先会报
```
Deprecated Gradle features were used in this build, making it incompatible with Gradle 7.0.
Use '--warning-mode all' to show the individual deprecation warnings.
```
研究了很久,最后在react native的issues里找到了解决办法
### 第一步
修改项目根目录下的 `android/build.gradle` 文件,替换文件中
```
classpath("com.android.tools.build:gradle:X.X.X")
```
到
```
classpath("com.android.tools.build:gradle:4.1.0")
```
### 第二步
修改根目录`android/gradle/wrapper/gradle-wrapper.properties`文件,替换
```
distributionUrl=https\://services.gradle.org/distributions/gradle-6.2-all.zip
```
到
```
distributionUrl=https\://services.gradle.org/distributions/gradle-6.5-all.zip
```
### 第三步
重新运行 `npx react-native run-android` 就可以解决这个版本错误问题了
## 第二个错误
我按照官方文档给的链接装的jdk8,不过因为调试上一个错误期间也卸载重装过也修改过环境变量,不知道
是不是这个原因导致的这个错误:
```
Could not find tools.jar
```
不过最后解决了。下面是解决办法:
### 第一步
首页需要安装 `choco` ([安装链接](https://chocolatey.org/install#install-with-cmdexe)),安装完成后运行:
```
choco install -y jdk8
```
### 第二步
在win10系统环境变量里新建一个环境变量:
> 变量名:`JAVA_HOME`

> 变量值:`C:\Program Files\Java\jdk1.8.0_211`

### 第三步
重新运行 `npx react-native run-android` 这个问题也解决了, 不过具体原因不是很清楚,推测是因为没有jre目录,因为重新用
`choco` 安装jdk以后多出一个 **jre1.8.0_271** 文件夹

---

这样就ok了,就可以编写基于react native的跨平台应用的.

