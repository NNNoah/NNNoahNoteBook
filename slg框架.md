# slg游戏框架

游戏大致功能分为:

1. 城建功能;
2. 多层大地图(ECS框架);
3. 卡牌战斗PVE;
4. 外围系统;

## 客户端

代码框架整体是基于服务的框架;

### 1.城建功能系统

### 2.外围UI系统

使用MVVM框架进行UI开发,设置

### 3.核心战斗

### 4.大地图
Lod:分层结构+ECS框架

显示层级(0-4)|逻辑层级(0-5)
-|-
0: 城建 | 0
1级地图 | 1-2
2级地图 | 3
3级地图 | 4-5
* 存在优化点

system:系统
MainObject:
以MainBuilding开头的system是城建相关的;
以World开头的system是大地图相关系统
city:主城
Base:
Filters:筛选器(城建使用)
GameEventState:城建事件
LodBaseSystem:



### 5.其他系统:

#### 1.音效功能:AudioSystem.cs服务

a.通过切面AOP代码切入对应逻辑代码位置,播放音效;
b.角色音效在对应的模型预制件上,通过timeline进行播放;