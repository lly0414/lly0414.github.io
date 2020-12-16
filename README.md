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
## 第三个错误
一般react native的项目都是需要导航的,官方推荐的的有基于js的 **react-navigation** 和基于双平台原生的 **react-native-navigation**

>react-native-navigation的性能好但是中文的文档和相关内容基本没有想要使用只能靠自己看官方的英文文档摸索,如果英文好当我没说....

>react-navigation使用起来很方便,百度也很容易找到翻译的文档和相关问题的解决方案,但是因为是基于js的可能流畅度没有那么高

我选择的是 **react-native-navigation**,所以这里安装的时候就发生了一个小错误,很简单,错误为:

```
Cannot fit requested classes in a single dex file (# methods: 66802 > 65536)
com.android.builder.dexing.DexArchiveMergerException: Error while merging dex archives:
The number of method references in a .dex file cannot exceed 64K.
```

经过百度,原因也很简单:

>引入react-native-navigation后,项目方法过多,超过65k个方法.
因为Android系统定义总方法数是一个short int,short int 最大值为65536,一个dex已经装不下了,需要个多个dex,也就是multidex.

解决方法为:
### 第一步
找到项目根目录下 `android\app\build.gradle` 文件,找到 `android`下的`defaultConfig`在里面添加`multiDexEnabled true`
### 第二步
同文件,找到`dependencies`在里面添加`implementation 'com.android.support:multidex:1.0.3'`

---

这样就ok了,就可以编写基于react native的跨平台应用的.

