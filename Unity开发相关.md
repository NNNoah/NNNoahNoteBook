
# Unity基础

## 1.摄像机

* 摄像机的2种投影方式:正交和透视
正交:无法看到物体到自己的距离,物体也不会随着距离而收缩，一般2D游戏或UI使用正交摄像机
透视:看物体会随着距离有大小的变化，一般3D游戏使用这种摄像机

* canvas: render mode为 overlay,会使UI渲染用在在最前面,导致特效看不到,修改特效渲染层级也没用;要能看到特效必然不能使用overlay模式

## 2.特殊文件夹

* Resources：Assets目录下面新建Resources文件夹，其中的所有资源，不论是否被场景用到，都会被打包到游戏中

## 3.资源相关

* AssetsBundle:压缩方式默认压缩成lzma格式(zip),资源占用较少,但解压较慢;lz4方式:资源相对大一点,但解压速度提升几倍;lz4hc方式:压缩速度比小于lz4,但解压速度高于lz4;
  
* 贴图资源:android压缩:etc1的压缩需要是POT(2的幂方),etc2的压缩需要是4的倍数;不透明的使用ETC格式，透明的使用ETC2格式

##游戏对象显示层级相关
* 影响显示层级主要因素:Camera Depth;对象layer层级;meshrenderer的sortinglayer和sortingorder大小;材质的

## 4.其他

* System.Enviroment.TickCount,上次系统启动经过的毫秒(计时器),是一个带符号的32位int;大约25天后数据变为负值,若使用记得处理负值

* status面板上显示有1.7kTris(三角面数) 以及 5.0k Verts(顶点数).这是空场景默认自带的天空盒Windows—>Lighting打开 Ligh下的Scene面板,不需要则把Skybox里的材质设为空
  
# Unity进阶

## 1.热更新及打包策略

1. 通常情况下建议1M-10M左右的AB包,ab包数量控制在一个合理的数值,越少性能越高
2. 根据依赖树进行的最优打包策略，公共资源单独打ab，独立资源打到一起;
3. shader字体等其他细碎并且需要常驻内存的资源打包到一起，启动游戏的时候常驻内存。
4. 根据项目实际需求将需要经常热更新的资源进行单独打包。

## 2.垃圾回收器GC (Gabarage Collector)

  **作用:将废弃的内存重新回收以便于再次使用的**

1. Unity游戏运行时内存占用分以下几部分:

* Mono堆：C# 代码
* Native堆：资源，Unity引擎逻辑，第三方逻辑。
* 库代码：Unity库，第三方库

2. GC的策略算法了一般有以下几种

* a. 引用计数:在每个对象上维护着一个字段来统计多个对象正在使用自己。当引用计数为0的时候，对象就可以从内存中删除了。
但是引用计数设计存在着一个致命的问题，那就是处理不好循环引用。比如：在一个界面里，父窗口持有了对子对象的引用，子对象又持有了父窗口的引用。这样的循环引用就会导致内存泄漏，即使不需要这2个对象了，也不会被删除。
* b. 根搜索:从一个根开始遍历所有的叶子，然后把所有不需要的叶子清除。

3. Mono在2.10版本前都是使用了贝姆垃圾回收（Boehm conservative collector）,在2.10后的版本中使用了一个叫“Simple Generational GC”（SGen-GC）的垃圾回收器
unity默认使用的贝姆垃圾回收,unity2019引入新的增量式垃圾回收:增量式垃圾回收需要使用新.NET 4.x Equivalent脚本运行时版本,同时勾选PlayerSettings-->OtherSettings-->Use incremental GC
注意:有分帧处理减缓峰值,有可能部分项目使用会有隐患

4. 触发条件

* 被动触发

```
a. 当垃圾存储到一定时候，会被动触发回收这些无用内存。
b. 堆分配时堆上的可用内存不足时触发GC。
```

* 主动调用

```
//GC提供的接口:
GC.Collect()
//unity提供的接口
Resources.UnloadUnusedAssets()卸载未使用的资源,内部本身就会调用GC.Collect()
Resources.UnloadAssets():卸载资源，全部直接删除
```

1. 产生垃圾的一些情况

* ①游戏对象,组件等
* ②缓存
* ③对象池
* ④list,字典,hash等容器
* ⑤字符串的创建等
* ⑥Debug.Log
* ⑦装箱拆箱
* ⑧StartCortine会产生少量垃圾。
  
```yield return 0;//会产生垃圾，int变量0被装箱。
=>>> yield return null
yield return new WairforSeconds(1f);//如果多次被调用，会产生很多。
==>>>WaitForSeconds delay =new WaitForSeconds(1f);//可以事先缓存起来
yield return delay;
```

* ⑨Linq和正则表达式在后台有装箱操作而产生垃圾，最好少使用。

## 优化

* 研究游戏优化相关资料;内存(逻辑优化,资源优化);GPU(DC,setpass,overdraw);CPU(逻辑)
apk,ipa包体积优化:1.压缩资源;打AB包;2.贴图POT优化;3.打图集;4.无透明度的贴图png转jpg;

1. 包体越小,用户才愿意下载(大多数手机存储大小都不够用);
2. 删除app时,基本都是从越大的应用开始删;

# Unity高级