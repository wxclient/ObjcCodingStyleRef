# 旺信iOS团队Objective-C编码风格参考

## Objective-C Coding Style Reference of Wangxin iOS Team

> Writing Objective-C code is like writing prose.

1. 缩进和代码块大小
	* 代码缩进
	* 单行代码的宽度
	* 单个函数的高度	
	* 在单个函数中适当换行
	* 在视图控制器中为代码分组
2. 注释
	* 注释的格式
	* 什么时候要注释
3. 空格
	* 一段示例代码
	* 需要和无需空格的地方
4. 花括号的风格
	* 三种基本风格
5. 命名
	* 文件命名
	* 类的命名
	* 方法命名
	* 变量命名
	* 缩写命名
6. to add

## 缩进和长度

### 代码缩进（`Required`）

代码缩进统一遵循Xcode默认值：4个空格。<br/>

如果使用Tab进行缩进，可以在Xcode - Prefereces - Text Editing中的Indentation选项卡中，将Tab的宽度设置为4个空格，与缩进宽度保持一致（默认值）。

### 单行代码的宽度（`Suggested`）

通常，一行代码最好不要超过80个字符。<br/>

同时，一行代码不应该超过编辑区域的可视范围，即不应该由于代码行过长，使得开发人员需要水平滚动来阅读整行代码。（`Required`）<br/>

比如下面这行代码就属于太长的范围：

```
		[[WXLoginBizLogic sharedInstance] saveLoginedUserInfo:loginId userid:userId nickname:nickName logintoken:loginToken loginSrvs:nil];
```

可以修改为如下代码：

```
        [[WXLoginBizLogic sharedInstance] saveLoginedUserInfo:loginId
                                                       userid:userId
                                                     nickname:nickName
                                                   logintoken:loginToken
                                                    loginSrvs:nil];
```

考虑到Apple官方建议在编写Objective-C代码时使用全称对变量进行命名，从而避免歧义、增加代码可读性，因此在代码单行不超过屏幕可视区域的情况下，可以适当增加到100个字符。

### 单个函数的高度（`Suggested`）

同上，原则上一个函数的高度（受行数和字体大小影响）不应该超过编辑区域，即不应该由于函数体过于庞大，使得开发人员需要垂直滚动来阅读一个完整的函数内容。（`Required`）<br/>

基于这点要求，*一个函数体（包括函数名和最外围的两个花括号）应该保持在 **50行** 以内（含）*。当一个函数超过50行时，则需要考虑是否将其拆分成多个更小的函数；或者邀请其它开发人员进行review，陈述超过50行的理由。

另外，当函数足够精简、作用足够明确的时候，也有助于我们聚焦问题、利于问题分析和性能优化等工作。

### 在单个函数中适当换行（`Required`）

大部分情况下，一个函数体内可能做了几件更小的任务单元。在这些不同的小单元之间，应该使用空行来进行恰当的分隔，从而提高代码的阅读体验。*这里需要注意的是划分的准确性*。<br/>

除了函数，在其它需要的地方（比如类的接口定义）也要适当换行，如下是一段摘自工程里的代码：

```
@property (retain, nonatomic) IBOutlet UIView * tableHeadView;
@property (retain, nonatomic) IBOutlet UIView * noDataView;
@property (retain, nonatomic) IBOutlet UILabel  * badgeLabel;
@property (assign, nonatomic) BOOL bHasLogout;
@property (assign, nonatomic) BOOL bHasInsert;
@property (assign, nonatomic) BOOL bHasDelete;

- (id)initWithQuery:(NSString*)path query:(NSDictionary*)query;

- (IBAction)pressAvatar:(id)sender event:(id)event;
- (IBAction)pressGotoFriendTab:(id)sender;
- (void)onReachabilityChanged:(NSNotification *)note;

@end
```

开头的是6行属性声明，可以分为两类；最下面的3个接口中，前两个是响应用户点击动作，与第3个也不属于同一类，所以应该添加两行空行：

```
@property (retain, nonatomic) IBOutlet UIView * tableHeadView;
@property (retain, nonatomic) IBOutlet UIView * noDataView;
@property (retain, nonatomic) IBOutlet UILabel  * badgeLabel;

@property (assign, nonatomic) BOOL bHasLogout;
@property (assign, nonatomic) BOOL bHasInsert;
@property (assign, nonatomic) BOOL bHasDelete;

- (id)initWithQuery:(NSString*)path query:(NSDictionary*)query;

- (IBAction)pressAvatar:(id)sender event:(id)event;
- (IBAction)pressGotoFriendTab:(id)sender;

- (void)onReachabilityChanged:(NSNotification *)note;

@end
```

此外，还有两点需要注意：一是析构函数dealloc中成员变量的释放顺序应该和接口定义中的声明顺序保持一致以便于检查（`Required`）；二是`onReachabilityChanged`方法从命名上看，是监听消息进行响应的，不应该作为接口暴露（`Required`）。

### 在视图控制器中为代码分组（`Required`）

如同一个函数中要为不同功能块换行分隔，一个ViewController中（不限于ViewController，适用于任何类）也有很多方法，而这些方法同样需要分类。

使用Xcode进行代码编写时，我们可以采用#pragma mark编译器指示符对方法进行分组，以便让代码有良好的组织方式，利于代码阅读和分析（比如通过Control+6组合键）。

通常，我们可以将一个ViewController的方法划分为如下几类（包含但不限于）：

* 视图控制器的生命周期
* 视图的生命周期
* 特定视图控件的初始化和布局
* 监听消息和解除监听
* 用户交互的响应方法
* 代理方法
* 其它需要分组的方法……

需要指出的是，为方法进行分组是必需的。比如当前群聊窗口有3000+行代码，如果所有的方法都是一个接一个紧凑挨着，就很不利于代码阅读和分析。因此，**当你决定要往某一个ViewController添加方法时，如果这个方法是属于新功能，那么请加上“#pragma mark - 功能描述”；如果是在既有功能中添加，请保持在原有编译器指示符范围内（如无则补上）**。

## 注释

### 注释的格式（`SDK Interface Required`）

基于最新的LLVM，在遵循一定的注释格式的情况下，我们可以得到自定义函数的Quick Help。<br/>

同时，我们还可以利用现有工具来自动化这个过程，并且将注释汇聚成最终文档。*这点对于我们的SDK工程有很大意义*，具体可以参考我的博文[《LLVM对注释的新增支持 @ WWDC 2013》](http://blog.csdn.net/jasonblog/article/details/9631687)。

### 什么时候要注释

当你一时想不到一个足以表达清楚的变量名时（`Required`），当你编写代码的思路没那么顺畅时（`Required`），或者其它任何你觉得需要的时候，请稍微停驻，通过增加注释来帮助自己梳理思路，避免下次自己回过头来看的时候完全不知道某个变量有何作用，当然也帮助其他开发人员更容易理解和维护代码。

## 空格

### 一段示例代码

需要使用空格来提高代码阅读体验的地方可能一下子枚举不完，不妨先通过旺信工程里的一段代码来建立初步认知：

```
//记录历史登录记录用户的token
+ (void)setToken:(NSString*)token forUser:(NSString*)userid
{
    NSError * error = nil;
    [WXKeyChainUtils storeUsername:userid andPassword:token forServiceName:[[NSBundle mainBundle] bundleIdentifier] updateExisting:YES error:&error];
    if( error || token==nil || token.length == 0 || userid == nil || userid.length == 0 )
    {
        WXERROR(@"setToken error = %@",error );
    }
}
```

上面的代码显得有点紧凑，风格上也稍显凌乱，对其稍作调整：

```
// 记录历史登录记录用户的|token|   /* 修改点1 */
+ (void)setToken:(NSString *)token forUser:(NSString *)userid   /* 修改点2 */
{
    NSError *error = nil;   /* 修改点3 */
    NSString *bundleIdentifier = [[NSBundle mainBundle] bundleIdentifier];  /* 修改点4 */
    
    /* 修改点5 */
    [WXKeyChainUtils storeUsername:userid
                       andPassword:token
                    forServiceName:bundleIdentifier
                    updateExisting:YES
                             error:&error];
    
    /* 修改点6 */
    if (error || /* 修改点7 */
        token == nil || /* 修改点8 */
        token.length == 0 ||
        userid == nil ||
        userid.length == 0)
    {
        WXERROR(@"setToken error = %@", error); /* 修改点9 */
    }
}
```

可以看出有如下几个修改点：

1. 修改点1：注释双斜杠后空一格；关键变量名token用竖线|符号突出。
2. 修改点2：指针类型的 *星号 和具体类型之间用空格隔开；同时，方法名开头的+/-号后面要空格。
3. 修改点3：指针类型变量，*星号后接变量名，无需空格；同时，变量尽量初始化。
4. 修改点4：不要在函数调用处直接使用函数返回值作为参数，不利于调试；同时，命名时尽量采用全称。
5. 修改点5：一个函数体内适当用空行分隔，提高可读性；函数调用太长时，换行对齐。
6. 修改点6：条件判断过多时，将所有条件放在一行不易于阅读和检查，建议换行对齐。
7. 修改点7：if后面跟圆括号要有空格。
8. 修改点8：算术/逻辑运算符与左右两边的运算参数之间要有空格。
9. 修改点9：用逗号分隔参数时，逗号与下一个参数之间要有空格；前后两个参数和两端花括号之间无需空格。

### 需要和无需空格的地方

* 方法类型+/-后要空格；方法返回值类型和方法名之间无需空格；
* 指针类型，类型和星号之间要空格；星号和变量名之间无需空格；
* 变量赋值，等号两边要空格；

下面相应的3行示例代码（摘自苹果官方）。

```
+ (id)arrayWithArray:(NSArray *)array;

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;

NSError *error = nil;
```

* 注释符号（斜线或星号）和注释正文之间要有空格；

```
// default is UIBarStyleDefault (blue)

/* Methods for manipulating text. */

/**
	使用用户名和密码进行登录。
	@param username 用户名
	@param pwd 密码
 */
```

* 运算符和运算参数之间要有空格，比如条件判断语句；
* 关键字与其它字符之间要有空格，比如if、while等关键字和后续字符之间；
* 函数参数之间要有空格，比如常见的；

```
BOOL flag = (param0 && param1) || (param2 && param3);

@property (assign, nonatomic) BOOL flag;

if (title.length > 0) {
	// Use the title.
}

NSString *str = [NSString stringWithFormat:@"Use the %@", title];

NSDictionary *dict0 = @{@"key" : @"value", @"key1" : @"value1"};

NSDictionary *dict1 = [NSDictionary dictionaryWithObjectsAndKeys:@"obj", @"key", nil];
```

* 其它觉得需要加空格来使代码更清晰、可读的地方；

# 花括号的风格

### 三种基本风格

我们团队接受如下3种花括号缩进风格：

```
    /* Xcode代码自动补齐的K&R风格，花括号跟在行末。 */
    if (<#condition#>) {
        <#statements-if-true#>
    } else {
        <#statements-if-false#>
    }
```

```
    /* 花括号另起一行的MS风格 */
    if (<#condition#>)
    {
        <#statements-if-true#>
    }
    else
    {
        <#statements-if-false#>
    }
```

```
    /* 中间风格 */
    if (<#condition#>) {
        <#statements-if-true#>
    }
    else {
        <#statements-if-false#>
    }
```

但不接受上面3种风格的混合体，比如：

```
    /* 混合风格 */
    if (<#condition#>) {
        <#statements-if-true#>
    } else
    {
        <#statements-if-false#>
    }

```

因此，修改他人代码时，需要遵循现有风格，或者修改成统一风格（`Required`）。

## 命名

> "There are only two hard things in Computer Science: cache invalidation and naming things." - Phil Karlton

良好、恰当的命名会让我们写代码就像写流畅的英文句子，可读性佳，在阅读代码的时候就能容易地理解代码的作用（即句子所表达的意思）。

在命名方面，Objective-C倾向使用完整的名称，这会使得写Objective-C代码就像是在写英文散文。

### 文件命名

| suffix | content  
| ------ | -------- 
| .h     | Header file, including Objective-C interface
| .m     | Objective-C implementation
| .mm    | Objective-C, mixed with C++ code
| .c     | C source code
| .cpp   | C++ souce code

通常，文件名与相应的类名保持一致，比如AppDelegate.h。如果是Category，则使用+号连接Category的名称，如NSString+md5.h。

### 类的命名

类名首先要是一个名词，并且需要清楚地表达出这个类（或者类的实例）的作用。该名词以大写字母开头，采用驼峰命名法（大小写）分隔不同单词。

今天在项目代码里发现有用动宾结构短语作为类名，比如一个Http请求类叫做GetConfigurationFile（GetConfigFileDataRequest可能更合适）。

正常代码中的类名不需要（不建议）加前缀，当你决定为一个类名加上前缀，则说明这个类是可以作为单独的、封装良好的、可以复用的组件或者框架等基础服务（`Important`）.

### 方法命名

方法名称以小写字母开头，同样采用驼峰命名风格。在对一个方法进行命名时，需要保证读起来像一条句子（`Very Important`）。这点很重要，因为方法的名称（定义、实现、调用等）是一个应用程序源代码中最主要的成分。



### 变量命名

### 缩写命名

