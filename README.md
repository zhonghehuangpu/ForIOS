# ForIOS
厚啪开发规范ForIOS

厚啪网络 Objective-C 编码规范


* 项目结构
    * HPTabBarController
        * 消息列表HPMessageListViewController
            * 推荐用户列表HPRecommandUsersViewController
        * 孤独超市首页HPLonelyShopViewController
            * 私聊控制器HPChatEmojiPicViewController
                * 地图HPMapView
                * 表情控制器HPEmojiViewController
                * 私聊控制器NTESSessionViewController
            * 搜索用户HPSearchUserViewController
            * “我想”人物列表HPWantCollectionViewController
        * 个人页HPProfileViewController
            * 照片详情HPPhotoDetailViewController
            * 修改个人信息HPProfileEditViewController
            * 选择照片控制器HPPickerViewController
            * 关注列表HPFollowViewController
            * 粉丝列表HPFansViewController
            * 设置HPSettingViewController
        * 登录HPLoginViewController
    * 网络请求:继承  HPBaseRequest
    * 控制器继承:HPBaseViewController
    * 底部弹出提示:HPMoreOperationViewController



======================





![项目结构](https://github.com/zhonghehuangpu/ForIOS/blob/master/readme1.jpeg)
<a name='TOC'/></a>目录
----
* [命名](#naming)
  * [基本原则](#naming-basic-principle)
  * [命名空间](#namespace)
  * [视图命名](#naming-viewcontroller)
  * [方法名](#naming-method)
  * [协议名](#naming-protocol)
  * [通知命名](#naming-notifications)
  * [临时变量命名](#naming-temporary-variable)
  * [常量命名](#naming-constant)
  * [大小写](#naming-match-case)
  * [缩写](#abbreviation)
  * [其他](#naming-others)
* [代码格式化](#formatting)
  * [空格](#spaces)
  * [花括号](#braces)
  * [折行](#line-wrap)
* [代码风格](#code-organization)
* [其他](#others)
  * [异常](#exception)

<a name='naming'/></a>命名
-----
### <a name='naming-basic-principle'></a>基本原则--务必遵守

* 仿照 Cocoa 风格来，使用长命名风格
* 每个头文件(.h文件)，必须要对该类作用做说明
* 使用 #pragma mark 来为方法分组 标记。
* 注释要求：
```C
不需要对外提供的方法和属性，不得在头文件(.h)中进行定义，头文件中定义了属性和方法，必须添加注释

头文件中对外提供的API，建议以方法参数的方式，不建议采用属性的方式

实现文件(.m文件)中定义的变量、属性和自定义方法，必须添加注释

枚举类型、宏定义、结构体等需要添加注释，枚举类型需要对各个枚举值添加说明
```
* 自定义命名风格采用功能描述，如本应用内，或本团队内框架制作，可选用HP作为前置缩写，放在类名前，用于标志为自定义完成类。
* 变量命名推荐的命名语素顺序是：最开头是命名空间简写，然后越重要、区别度越大的语素越要往前放。经典的结构是：作用范围+限定修饰+类型。例：

```C
extern ushort APIDefaultPageSize;        // 还行，能明白意思了
extern ushort APIDefaultFetchPageSize;   // 加上些限定更好一些
extern ushort APIFetchPageSizeDefault;   // 再好些，把重要的往前放

HPToolbarComment    // 不推荐
HPCommentToolbar    // OK，把类型（toolbar）置后
```
* 函数长度（行数）不应超过2/3屏幕，禁止超过70行。
: 例外：对于顺序执行的初始化函数，如果其中的过程没有提取为独立方法的必要，则不必限制长度。
* 单个文件方法数尽量不应超过30个
* 不要按类别排序（如把IBAction放在一块），应按任务把相关的组合在一起
* 禁止出现超过两层循环的代码，用函数或block替代。
* 单页代码VC，通常不要超过1000行，300-600行为最佳阅读体验。
### <a name='namespace'></a>命名空间
* 类名、protocols、C 函数、常量、结构体和枚举应带有命名空间前缀；
* 类方法不要带前缀，结构体字段也不要带前缀


### <a name='naming-viewcontroller'></a>视图命名

为了举例，我们假定有 `User`、`Tag`、`Category` 这几种 model 类型。

对象展示一般分列表和单个详情，其 view controller 分别使用 **Model**ListController 和 **Model**DetailController，推荐的语素顺序是：`Model名 + 限定与修饰 + ListController|DetailController`。举例说明：

```
// OK
TagUserUsedListController
TagInCategoryListController
CategoryDetailController

// 不推荐，列表推荐统一使用 ListController，不需要指明是 table view 还是 collection view
UserFollowerTableViewController

// OK
UserFollowerListController

// 不推荐
UserLikedTagListController

// OK，把显示的对象放在第一位
TagUserLikedListController

// 糟糕，如果是 view controller，必须以 Controller 或 Displayer 结尾
TagListView
```

经常为了便于多个界面复用，我们会把 model 的显示统一在一个 view controller 中，在其他界面嵌入这个 view controller。我们把这类专门管理显示的 view controller 叫做 `displayer`。如：

```
UserListDisplayer
TagListDisplayer
```

UIView 级别的组件不要以 Controller 或 Displayer 结尾，如果起到管理作用可以使用 control 结尾。


**动机-为何如此？**
> 把 model 名放在首位（如 TagUserLikedListController 而不是 UserLikedTagListController）的主要考量是便于搜索。因为 Xcode 不支持乱序搜索，关键词只能从前往后才会有结果。
>
> 如果限定词在前，因为不同人理解差异，自己也会遗忘，这个限定词经常是输入不能的，只能搜 TagList 再从列表中查找，等于第一位的查找语素就废掉了。当 model 类型在第一位时，基本上熟悉这个项目的人都清楚要查找的视图显示的是什么类型，第一位正确了，后面添加/修改限定就很方便了。
> 
> 另一个便利的场景是参考之前界面实现另一个界面时，查找的大都是相同类型的界面，如实现 UserFollowerListController 参考 UserFollowingListController；而相同限定的场景比较少见，像 UserLikedTagListController 参考 UserLikedCategoryListController 的可能性就较少。
>
> PS: 如果你不用 Xcode 的 Open Quickly（默认快捷键 Command+Shift+O），强烈建议你改一下习惯
>


### <a name='naming-method'></a>方法名
* 以 `alloc`、`copy`、`init`、`mutableCopy`、`new` 开头的方法要注意，它们会改变ARC的行为，过多的调用和引用会引发一系列内存泄漏与引用计数错乱问题。
* 以 `get`、`set` 开头的方法有特殊的意义，不要随意定义。
  1. set 是属性默认的设置方法，如果函数不是为了设置类成员，则不要用 `set` 开头，可用 `setup` 替代。
  2. get 和属性方法无关，但在 Cocoa 中，其标准行为是通过引用传值，而不是直接返回结果的。欲获取变量，直接以变量名为名，如：`userInfomation`，而不是 `getUserInfomation`。

例：

```Objective-C
// OK
- (NSString *)name;

// 糟糕，应用上面的写法
- (NSString *)getName;

// OK，但极少使用
- (void)getName:(NSString **)buffer range:(NSRange)inRange;


// OK
- (NSSize)cellSize;

// 糟糕，应用上面的写法
- (NSSize)calcCellSize;
 

// 对 controller 做一般设置，OK
- (void)setupController;

// 列出具体设置的内容，更好
- (void)setupControllerObservers;

// 糟糕，set 专用于设置属性
- (void)setController;
```

```Objective-C
// 来自官方文档
insertObject:atIndex:    // OK
insert:at:               // 不清晰，插入了什么？at 具体指哪里？
removeObjectAtIndex:     // OK
removeObject:            // OK
remove:                  // 糟糕，什么被移除了？
```

### <a name='naming-protocol'></a>协议名
好的协议名应能立刻让人分辨出这不是一个类名，除了以常用的 delegate、dateSource 做结尾外，还可以使用 …ing 这种形式，如：`NSCoding`、`NSCopying`、`NSLocking`。


### <a name='naming-notifications'></a>通知命名
基本命名格式是：`[与通知相关的类名] + [Did | Will] + [UniquePartOfName] + Notification`，例：

```Objective-C
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```

### <a name='naming-temporary-variable'></a>临时变量命名
* 临时变量可以写得很短，如 i、k、vc 这样；
* 临时变量可以使用匈牙利前缀，但数据类型不可以作为前缀：

```C
// OK
wCell, vcMaster, vToolbar

// 糟糕，数据类型作为前缀
bool_switchState, floatBoxHeight
```

推荐的前缀：

前缀 | 含义
-----|-----
ix   | 序号，起始为0
in   | 序号（自然数范围），起始为1
if   | 类型为浮点的“序号”
x    | 坐标
y    | 坐标
w    | 宽度
h    | 高度
vc   | 视图控制器
v    | 视图

### <a name='naming-constant'></a>宏常量命名
常量约定了以下：

宏分类      | 宏类名
------------|-----
工具宏      | HPUtilMacros
账号ID宏    | HPKeyMacros
UI宏        | HPUIMacros
接口宏      | HPURLMacros
业务宏      | HPBusinessMacros


### <a name='naming-match-case'></a>大小写
* 类名采用大驼峰（`UpperCamelCase`）
* 类成员、方法小驼峰（`lowerCamelCase`）
* 局部变量大小写首选小驼峰，也可使用小写下划线的形式（`snake_case`）
* C函数的命名用大驼峰

### <a name='abbreviation'/></a>缩写
可以使用广泛使用的缩写，如 `URL`、`JSON`，并且缩写要大写。但像将`download`简写为`dl`这种是不可以的。


```Objective-C
// OK
ID, URL, JSON, WWW

// 糟糕
id, Url, json, www

destinationSelection       // OK
destSel                    // 糟糕
setBackgroundColor:        // OK
setBkgdColor:              // 糟糕
```


### <a name='naming-others'></a>其他
i，j专用于循环标号

为私有方法命名不要直接以“_”开头，而应以“命名空间_”开头。

<a name='formatting'/></a>代码格式化
-----
### <a name='spaces'></a>空格
类方法声明在方法类型与返回类型之间要有空格。

```Objective-C
// 糟糕
-(void)methodName:(NSString *)string;

// OK
- (void)methodName:(NSString *)string;
```

条件判断的括号内侧不应有空格。

```C
// 糟糕
if ( a < b ) {
    // something
}

// OK
if (a < b) {
    // something
}
```

关系运算符（如 `>=`、`!=`）和逻辑运算符（如 `&&`、`||`）两边要有空格。

```C
// OK
(someValue > 100)? YES : NO

// OK
(items)?: @[]
```

二元算数运算符两侧是否加空格不确定，根据情况自己定。一元运算符与操作数之前没有空格。

多个参数逗号后留一个空格（这也符合正常的西文语法）。


### <a name='braces'></a>花括号
方法的花括号推荐另起一行。方法内部需要写在一行。

```Objective-C
  - (void)methodName:(NSString *)string {
   ↑空格                              ↑空格，推荐花括号在一行
      if () {
   空格↑  ↑空格，花括号不要另起一行
      }
      else {
要换行↑ ↑空格，花括号不要另起一行
      }    
  }
```

**动机**
> Xcode 默认的花括号位置是这样的：方法内部的各种补全都是在同一行的；方法定义的比较混乱，默认模版另起一行，但从 Interface Builder 中连线生成的方法在同一行的。
> 
> 考虑到 Xcode 的默认行为，方法内部要另起一行，方法所在行不强制定死。另外，模版可以定制，而 IB 生成的代码不可定制，所以不另起一行的写法优先。
>
> 另起一行的写法在代码折叠后非常难看。
> 

### <a name='line-wrap'></a>折行
与多数其他规范不同，不建议手动折行，代码行过长可调整Xcode浏览宽度设置。

**动机**
> 手动折行的效果严重宽度依赖于窗口宽度——窗口过宽浪费宝贵的屏幕空间，较窄时可能无法阅读。而且 Xcode 自动折行的效果还是不错的。

<a name='code-organization'></a>代码风格举例
----


尽早返回错误：

```Objective-C
// 为了简化示例，没有错误处理，并使用了伪代码

// 糟糕的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    Task *aTask;
    if ([path isURL]) {
        if ([fileManager isWritableFileAtPath:path]) {
            if (![taskManager hasTaskWithPath:path]) {
                aTask = [[Task alloc] initWithPath:path];
            }
            else {
                return nil;
            }
        }
        else {
            return nil;
        }
    }
    else {
        return nil;
    }
    return aTask;
}

// 改写的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    if (![path isURL]) {
        return nil;
    }
    
    if (![fileManager isWritableFileAtPath:path]) {
        return nil;
    }
    
    if ([taskManager hasTaskWithPath:path]) {
        return nil;
    }
    
    Task *aTask = [[Task alloc] initWithPath:path];
    return aTask;
}
```

<a name='others'></a>其他
------

### <a name='exception'></a>异常
* 作为被调用模块的维护者，当被调用不当时（参数有问题、不和时宜），如何处理需要考虑（抛出异常还是返回错误状态）；
* 不要依赖 try catch，它不是代替你做检查、填补遗漏的工具。


