# 组件化依赖版本统一
Android开发中，组件化开发中，经常会出现好几个module，这些module中可能都对一些包有依赖，因此需要统一其版本。

统一版本的方案如下：

## 在project下的build.gradle中设定依赖包的版本：
```
ext {
    // Sdk and tools
    minSdkVersion = 12
    targetSdkVersion = 25
    compileSdkVersion = 25
    buildToolsVersion = '25.0.2'

    // App dependencies
    supportLibraryVersion = '25.2.0'
    constraintVersion = '1.0.0-beta5'
    junitVersion = '4.12'

    //third dependencies
    retrofitVersion = '2.2.0'
    gsonVersion = '2.8.0'
}
```

## 在module中引用版本号

```
dependencies {
    ...
    compile "com.android.support:appcompat-v7:$rootProject.supportLibraryVersion"
    testCompile "junit:junit:$rootProject.junitVersion"
    //retrofit
    compile "com.squareup.retrofit2:retrofit:$rootProject.retrofitVersion"
    compile "com.google.code.gson:gson:$rootProject.gsonVersion"
}
```