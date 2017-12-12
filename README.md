# FlavorDimensionsDemo
FlavorDimensionsDemo 构建变体演示代码

## What：
我们知道Flavor的作用是将debug和releas维度扩大，而它是基于flavor的一个扩展，另维度再次扩大，它是一个属性，全名叫做：flavorDimensions，看名字可知，要用它，必须先要有会用Flavor。可能现在你还有点迷糊，下面看个例子。

## Think：
在理解了flavor的前提下（[Flavor基本使用](http://blog.csdn.net/user11223344abc/article/details/78768987)），我们来看一个例子：

### 原有：
```java

    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.release
        }

        debug {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.debug
        }
    }


    productFlavors {
        baidu {
            manifestPlaceholders = [CHANNEL: "baidu"]
        }

        xiaomi {
            manifestPlaceholders = [CHANNEL: "xiaomi"]
        }
    }
```

目前是四个渠道：
![](https://wx4.sinaimg.cn/mw1024/0061ejqJly1fmdsg9uuyfj311y0jadjf.jpg)

### 添加flavorDimensions后：
```java
    flavorDimensions("vip", "free")

    productFlavors {

        vip {
            dimension "vip"
        }

        free{
            dimension "free"
        }

        baidu {
            manifestPlaceholders = [CHANNEL: "baidu"]
            dimension "vip"
        }

        xiaomi {
            manifestPlaceholders = [CHANNEL: "xiaomi"]
            dimension "free"
        }
    }
```

目前是几个渠道？数数，8个。
![](https://wx4.sinaimg.cn/mw1024/0061ejqJly1fmdsvc2rkwj311y0jadj8.jpg)

我们先根据现象看本质，首先，多出了了几个，我这里罗列出来：
![](https://wx2.sinaimg.cn/mw1024/0061ejqJly1fmdszbos8mj30ga06sac6.jpg)

逻辑上就是：  

由：

debug/releas（2） * 渠道（2） = 4个

变成了：

debug/releas（2） * 渠道（2） * 新增维度vip/free(2) = 4 * 2 = 8个


### 然后我们再从代码理解上来：
可以看到，这里其实是一个绑定的关系：
首先我们定义了flavorDimensions，它就像是一个标记池，而这一段的意思就是新增2个业务维度，flavorDimensions内的字符串("vip", "free")就像是一个证书，而productFlavors内的vip/free才是一个切切实实在在的维度，只有新增的维度先存在了，后面的渠道引用这个维度的时候才会维度的扩展，说白了，就是你vip和free这俩个代码段先写出来了，后面的什么小米百度这些渠道包在新增维度的时候，打包才能打出不同版本的包来：
```java
    flavorDimensions("vip", "free")

    productFlavors {

        vip {
            dimension "vip"
        }

        free{
            dimension "free"
        }
		
		......
    }
```

## Summary：
总结下，做这种打包的时候，一定要理解维度也是存在于favor代码域内的，它看上去就像是一个渠道，但他不是一个具体的渠道，我们必须定义它的理由是，让接下来的渠道包或者业务包用来打上新增的业务维度。

## Demo：
[github](https://github.com/zj614android/FlavorDimensionsDemo)

## Thanks：
[官方ProductFlavor配置文档](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html)
[官方BuildType配置文档](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html)
[一篇不错的博客](http://zheteng.me/android/2016/02/29/flavors-with-gradle/)


