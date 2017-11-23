# android gradle 配置
文本不介绍其他打包工具相比的优劣了，这里主要说一下基本使用[有些内容还真的是花了些功夫]。

## 配置编译
配置SDK要配置compileSdkVersion、buildToolsVersion：
```groovy
...
android {
    compileSdkVersion 25
    buildToolsVersion 25.0.2
    ...
}
```
根据自己的需要设置版本号

## 配置android下的defaultConfig
defaultConfig其实也属于ProductFlavor[这个后面再说]. 根据名称，这就是默认配置。先看代码
```groovy
android{
    defaultConfig {
        applicationId "com.group.app"   //app的packageName
        minSdkVersion 16
        targetSdkVersion 25
        versionCode 1
        versionName 1.0.0
        multiDexEnabled true            //适配方法数超过64K
        ndk{}                           //设置ndk

        buildConfigField "String", "API_HOST", "http://www.yourhost.api" //这里就配置了一个String类型名字为API_HOST的常量，可以通过BuildConfig.API_HOST来访问
        ...
    }
}
```

## 配置signingConfigs
就在这里配置打包需要的签名信息
```groovy
android{
    signingConfigs{
        prod{
            storeFile file(yourKeyStoreFile)
            storePassword "your keystore password"
            keyAlias "your key alias"
            keyPassword "your key password"
        }
    }
}
```

## 配置buildTypes
setSigningConfig
 setProguardFiles
 setShrinkResources
 setRenderscriptOptimLevel
 setMinifyEnabled
 setDebuggable

 示例：
```groovy
android {
    buildTypes {
        release {
            signingConfig signingConfigs.prod
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), file('proguard-rules.pro')
        }
    }
}
```

## 配置productFlavors
这里主要是为了打多个包而设置的，生产中常有很多渠道包[豌豆荚、百度、应用宝等等].渠道包中也可以做一些特殊化的配置。给个示例：
```groovy
android {
    productFlavors {
        baidu{}
        qihu360{}
        xiaomi{}
        wandoujia{}
        bugly {
            applicationId "com.group.app_1"
        }
    }
}
```
上面列出了5个渠道包，bugly这个包需要做个特殊处理，将applicationId 设置为“com.group.app_1”, 而其他渠道为默认的"com.group.app"。

这里再以case补充说明一下：
 - 如果bugly这个渠道包要求使用的签名信息也不一样，那应该怎么做？
这种情况下，首先需要buildTypes里面的signingConfig删除，然后在productFlavors中指定：
```groovy
android {
    productFlavors.all { flavor ->
        flavor.signingConfig = name == "bugly" ? signingConfigs.bugly_prod : signingConfigs.prod

        flavor.manifestPlaceholders = [UMENG_CHANNEL : name]
    }
}
```
上面的代码中设置了bugly的包签名使用signingConfigs.bugly_prod，其他的渠道包使用signingConfigs.prod。

- 后面的manifestPlaceholders是干什么的？
这是为AndroidManifest.xml中以变量形式引入数据准备的。意思是准备了一个名为UMENG_CHANNEL值为渠道名的变量。看一下AndroidManifest.xml的使用：
```xml
<application
    ...>
    <meta-data
            android:name="UMENG_CHANNEL"
            android:value="${UMENG_CHANNEL}" />
</application>
```
OK，如果说application中label、icon需要动态设置的话，也可以根据这种方式来实现。