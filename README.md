## What：
我们知道Flavor的作用是将debug和releas维度扩大，而它是基于flavor的一个扩展，另维度再次扩大，它是一个属性，全名叫做：flavorDimensions，看名字可知，要用它，必须先要有会用Flavor。可能现在你还有点迷糊，下面看个例子。

## Think：
在理解了flavor的前提下（[Flavor基本使用](http://blog.csdn.net/user11223344abc/article/details/78768987)），我们需要明白一个概念—————**维度！**

维度在gradle中的体现是：**flavorDimensions**，譬如：
> flavorDimensions("money", "channel")

这是一个正确的维度扩展思路

- money 
场景，一个APP，我们推出收费和免费的版本。

- channel 
场景，不同的市场渠道。

根据这个思路来改代码：

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
android{
   
    ... 

    flavorDimensions("money", "channel")

    productFlavors {

        vip {
            dimension "money"
        }

        free{
            dimension "money"
        }

        baidu {
            manifestPlaceholders = [CHANNEL: "baidu"]
            dimension "channel"
        }

        xiaomi {
            manifestPlaceholders = [CHANNEL: "xiaomi"]
            dimension "channel"
        }
    }

    ...
}
```

目前是几个渠道？8个。
![](https://wx1.sinaimg.cn/mw690/0061ejqJly1fme2t9j1krj309u0ja0u0.jpg)


逻辑上就是：  

由：

debug/releas（2） * 渠道（2） = 4个

变成了：

debug/releas（2） * 渠道（2） * 新增维度vip/free(2) = 4 * 2 = 8个

如下图所示：
![](https://wx1.sinaimg.cn/mw690/0061ejqJly1fme35m4akzj30iq0a3jro.jpg)


### 然后我们再从代码理解上来：
- 只要你定义了flavorDimensions，那么你后面的每个flavor就必须要写"dimension"这个属性。
- baidu和xiaomi是属于渠道这个维度，所以它的dimension是channel。
- vip和free这俩个flavor是属于money这个维度（收费与免费），所以它的dimension是money。

## Error：
一个常规性的思维误区，类似这么定义一个flavorDimensions：

```java
flavorDimensions("vip", "free")
```
vip和free实际上是属于同一个维度，即“money”，所以这么定义是不对的。
这也是我最开始犯的思维误区。


## Demo：
[github](https://github.com/zj614android/FlavorDimensionsDemo)

## Thanks：
[官方ProductFlavor配置文档](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html)
[官方BuildType配置文档](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html)
[不错的博客1](http://zheteng.me/android/2016/02/29/flavors-with-gradle/)
[不错的博客2](http://ask.android-studio.org/?/article/99)




