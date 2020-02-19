# AndroidxAndSupport-
Compatibility mode of androidx and support 

## 前言
```
此方案是个暴力方案，但是绝对也是通用方案的一个思路；我自己从头到尾在实际项目中已经做过验证。
```

## 心路历程
```
  这些没有什么实际意义，只是记录一下项目经历。
  前段时间做项目，遇到一个主要功能的示例工程是基于Androidx库开发，而且功能非常之多；然后大哥来了，说咱们这个要封装SDK对接RN，由于我本人对RN是完全不了解，所以我too young too simple的相信了同事的话，先封装SDK,弄完再对接RN。
  等到对接RN的时候傻眼了，RN-0.60以下无法兼容androidx；RN的大哥和我都考虑了升/降版本的工作量，发现都是巨大无比的，所以我的第一个兼容性的想法就是，把我要做的SDK和引用到的androidx的资源打到一个aar包里面，给大哥们用。
  原理很简单，所有的功能都是那些类一个个垒起来的，要保证功能能用的话，只要保证那些功能所调用的类都在，理论上就不会有啥问题。
  然后我做了第一个尝试，把External libraries中androidx相关的jar包都拷出来，放到我sdk module的libs下，把sdk module的gradle里面的依赖都去掉，统一引用libs的依赖；编译没问题，万事大吉，把这些兄弟姐妹一起打了个aar包。
  然后我在新项目里面引用这个aar包，发现这个兄弟姐妹一家亲的包和support里面的一些类有冲突，这时转机来了；大哥说先不搞这个了，然后转头去弄其他的去了。
  再然后兜兜转转，还是需要处理androidx和rn-0.60以下版本的兼容，然后我就捡起来之前没做完的工作了
```

## 先简略的说第一步：将资源打包在一起
```
这么做的一个先决条件是你的module一定是‘com.android.library’,因为是要被打包的；如果不是，请拆出一个library出来～
这个主要是四个步骤：
  - 将External libraries中涉及到androidx的的jar都拷出来；注意默认命名都是class.jar，拷出来最好是按照原始包/版本命名，避免记错
  - 将拷出来的jar都放到要调试module的libs中，并将module的gradle中的maven依赖，都去掉，改成引用libs；然后测试功能是否能正常跑通
  - 配置一个本地maven（nexus的简单又方便），将module打包成aar
  - 新建一个项目，去掉所有的自动引入的依赖，只依赖上面打好的aar包，随便调一个aar里面的方法，试试能不能调用成功；如果出现class not found/not defined这种错的话，检查一下基本是打包打漏了
```

## 再简略的说第二步：去除和support的冲突
```
当控制台打印一堆，support.*和androidx.* duplicate的时候，就是干这个的时候了
这个步骤是非常无聊的一个过程，就是不停的解包（jar -xvf）、删除控制台报冲突的class文件、再重新打包(jar -cvf)
  - 比较多的应该是appcompat相关的
```
