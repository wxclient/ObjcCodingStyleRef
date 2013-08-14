# 旺信iOS团队Objective-C编码风格参考

## Objective-C Coding Style Reference of Wangxin iOS Team

1. 缩进和代码块大小
	* 代码缩进
	* 单行代码的宽度
	* 单个函数的高度	
	* 在单个函数中适当换行
2. 注释
	* 注释的格式
	* 什么时候要注释
3. 空格
	* 哪些情况需要加空格
4. 花括号的风格
	* 三种基本风格
5. 命名
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

### 在单个函数中适当换行（`Required`）

大部分情况下，一个函数体内可能做了几件更小的任务单元。在这些不同的小单元之间，应该使用空行来进行恰当的分隔，从而提高代码的阅读体验。*这里需要注意的是划分的准确性。*<br/>

## 注释

### 注释的格式

基于最新的LLVM，在遵循一定的注释格式的情况下，我们可以得到自定义函数的Quick Help。<br/>

同时，我们还可以利用现有工具来自动化这个过程，并且将注释汇聚成最终文档。*这点对于我们的SDK工程有很大意义*，具体可以参考我的博文[《LLVM对注释的新增支持 @ WWDC 2013》](http://blog.csdn.net/jasonblog/article/details/9631687)。

### 什么时候要注释

当你一时想不到一个足以表达清楚的变量名时（`Required`），当你编写代码的思路没那么顺畅时（`Required`），或者其它任何你觉得需要的时候，请稍微停驻，通过增加注释来帮助自己梳理思路，避免下次自己回过头来看的时候完全不知道某个变量有何作用，当然也帮助其他开发人员更容易理解和维护代码。

## 空格

### 哪些情况需要加空格

需要使用空格来提高代码阅读体验的地方可能一下子枚举不完，就先通过一段旺信工程里的代码有个认知：

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

然后对上面的代码稍作调整：

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
2. 修改点2：指针类型的 *星号 和具体类型之间用空格隔开；同时，方法名开头的+-号后面要空格。
3. 修改点3：指针类型变量，*星号后接变量名，无需空格；同时，变量尽量初始化。
4. 修改点4：不要在函数调用处直接使用函数返回值作为参数，不利于调试；同时，命名时尽量采用全称。
5. 修改点5：一个函数体内适当用空行分隔，提高可读性；函数调用太长时，换行对齐。
6. 修改点6：条件判断过多时，将所有条件放在一行不易于阅读和检查，建议换行对齐。
7. 修改点7：if后面跟圆括号要有空格。
8. 修改点8：算术/逻辑运算符与左右两边的运算参数之间要有空格。
9. 修改点9：用逗号分隔参数时，逗号与下一个参数之间要有空格；前后两个参数和两端花括号之间无需空格。

## 花括号的风格

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

所以，当你修改他人代码时，要嘛遵循现有风格，要嘛修改成统一风格（`Required`）。

## 命名
