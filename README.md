# swift-project-starter
A comprehensive, open-source guide for Swift developers, covering language fundamentals, best practices, and practical development techniques from beginner to advanced levels.

## 关于开发一个新的swift app

以个人的开发经历，记录一些2024年末开发一个pure Swift APP的心得体会。

上一个开发的app，最低支持的iOS版本为12.0，如今使用最新的Xcode（ 16.2 ）创建项目，默认最低支持`iOS15.6`，可以用到很多新的api，个人的经验知识可以得到一些提升。
下面从几个方面说明一下一个新的APP我们需要如何搭建规划。

### 项目架构
一个老生常谈的问题，使用什么架构来组织项目：MVC还是MVVM或者其他架构。
从写了这么多年代码来看，在项目初期，特别人开发人数不多的情况下，没必要做过多架构设计，难道用MVC，有经验的iOS开发写不出易于维护的代码吗？就算架构设计做的好，有的人照样能写成屎山；所以重要的不是架构设计，而是编写代码习惯：良好的命名，代码结构分层，必要的注释也能够解决维护和让他人易于看懂的问题。

可以不拘泥于MVC还是其他什么架构，app上有什么？
1. Controller
2. 路由跳转
3. 视图view
4. 用户操作，网络请求，逻辑处理
5. 其他（国际化，性能等）

把一个APP的开发看成一个乐高积木搭建，大的模块也是由小的原件拼装完成，对功能进行合理拆分，易于组装和拆解重组就够了。架构设计里面有个观点我很赞成：
> 没有坏的架构，只有适合当前项目的架构，架构是可迭代升级的，而不是一成不变的。

项目初期，没必要对各种基础进行多层封装，严格按照某种方式来实现功能。一方面，轻装上阵，小步快跑，项目表现良好，人员增加，自然可以在对项目架构进行升级迭代。

### 必要工作

前面说了没必要过度设计封装，那是不是可以什么都不设计呢？答案肯定是否定的，有一些准备工作，越前面做，后面维护成本越小。下面列表了一些必要的前期准备设计工作。

1. 字体，颜色，风格
2. 国际化
3. 数据存取一致，如userDefault
4. 日期
5. 资源管理，image，plist
6. 路由
7. 埋点

这些点，前期约定好，统一入口调用，可以省去后期再替换时的巨大工作量。特别是如国际化和字体，颜色等，当写到后期，突然要变更字体，而项目中使用的都是杂乱声明的，替换工作谁想干，而且无意义。

### TODO

# 经验记录
## UI
1. UIButton Configuration使用：[踩坑高亮时title颜色](https://github.com/HeathWang/swift-project-starter/blob/master/UI/UIButton%20Configuration.md)

## 效率