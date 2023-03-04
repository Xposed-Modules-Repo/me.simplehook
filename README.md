 **这里是SimpleHook的hook部分代码**

# simpleHook使用说明

**中文文档**|[English](README_EN.md)

> [simpleHook.apk](https://wwp.lanzoub.com/b0177tlri)(密码：simple)
>
> TG交流群: @simpleHook
>
> 本软件主打简单，如名字一样，如果你追求更复杂的hook操作，推荐使用 [jsHook（你可以实现更复杂的功能）](https://github.com/Xposed-Modules-Repo/me.jsonet.jshook)、[曲境（电脑端浏览器操作）](https://github.com/Mocha-L/QuJing)；如果你追求更多的扩展功能，推荐使用算法助手等等类似应用
>
> 功能概述：自定义返回值、参数值等，记录常见各种加密算法、toast、dialog、popupwindow、JSONObject创建增加等


## 1. 功能说明

### 页面介绍

**首页**

<img src="images\main_home_screenshot.png" width = "200" />

点击加号，可添加配置，点击添加配置进入下面页面

**配置页面**

<img src="images/config_screenshot.png" width = "200" />

点击‘搜索样式’图标，可进入AppList页面，进行选择应用

点击‘下载样式’图标，可保存配置

点击右下角加号，可在弹出窗口填写配置

<img src="images\config_dialog_screenshot.png" width = "200" />

有多种模式可以选择，输入类名前建议了解设置页面（smali to config），它可以简化填写

**扩展页面**

<img src="images\main_extension_screenshot.png" width = "200" />

**具体功能**

<img src="images\extension_main_features_shot.png" width = "200" />

点击“播放样式”按钮，可打开悬浮窗（需要授予悬浮窗权限），然后打开目标应用，可以显示一些信息（开启了打印参数值、返回值、扩展页面大部分功能）

悬浮窗

<img src="images\main_extension_print_dialog.png" width = "200" />

## 2.自定义Hook编写规则

下面是编写规则：（你可以下载*[HookTest.apk](https://github.com/littleWhiteDuck/HookTest/releases)*，此App应用了所有情况，并内附有配置）

> 使用前请先了解设置页【smali转配置】，它可以简化你的操作(配合MT管理器等逆向分析软件)

### 简要的基本介绍

- 支持Java语法和Smali语法填写配置信息

  ```java
  // java
  me.simplehook.MainActivty
  // smali
  Lme/simplehook/MainActivity; //一定要有 --> ; <--
  ```

- 支持基本类型和其它类型参数

  ```java
  // 类型 主要用于填写参数类型和变量类型
  // 基本类型你可以使用java语法这样填
  boolean int long short char byte float double
  // 基本类型你也可以使用smali语法这样填
  Z I J S C B F D
  // 其他类型你可以使用java语法这样填
  java.lang.String android.content.Context 
  // 其他类型你也可以使用smali语法这样填
  Ljava/lang/String; Landroid/content/Context; //一定要有 --> ; <--
  ```


### 结果值的填写规则

  > 此处应注意，本软件不像其他软件一样需要填写返回值、参数值类型，本软件并不需要，你只需要**按照规则填写**，自动判断

#### 2.1. 基本类型

| 类型(java、smali)  | 值的例子             | 注意事项                       |
| ------------------ | -------------------- | ------------------------------ |
| 布尔值(boolean、Z) | true、false          |                                |
| 整数(int、I)       | 1、2、3              |                                |
| 长整型(long、J)    | 1l、120000L、123456l | 要注意：数字 + L               |
| 短整型(short、S)   | 1short、2short       | 要注意：数字 + short           |
| 字符(char、C)      | 195c                 | 要注意：符合char类型的字符 + c |
| 字节(byte、B)      | 2b、3b               | 要注意：符合byte类型的字符 + b |
| 单浮点(float、F)   | 2f、3f、3.0f         | 要注意：数字 + f               |
| 双浮点(double、D)  | 2d、3d、3.0d         | 要注意：数字 + d               |

#### 2.2. null

> 其他类型只能返回null(字符串除外)，null

#### 2.3. 字符串

##### 2.3.1 一般情况

> 不符合基本类型和null的全部转化为字符串类型

##### 2.3.2 特殊情况

| 特殊的字符串 | 值的例子                         | 注意事项                                                     |
| ------------ | -------------------------------- | ------------------------------------------------------------ |
| 数字         | 111s, 2002s                      | 常见于 "111111" 这种，但是本软件你需要在数字后面加入s，如果你不加s，会被转成数字，可能导致目标应用崩溃 |
| 布尔         | trues、falses                    | 常见于 "true" 、"false" 这种，但是本软件你需要在布尔值后面加入s，如果你不加s，会被转成布尔值，可能导致目标应用崩溃 |
| null         | nulls                            | 常见于 "null"这种，但是本软件你需要在null后面加入s，如果你不加s，会被转成null，可能导致目标应用空指针 |
| 空字符串     | 英文单词'empty' 或者中文汉字'空' | 如果你直接填空，将无法保存配置，这样做是为了预防你在使用时不填修改值导致无法正常Hook |

##### 2.3.3 随机文本返回值

> 仅用于返回值，返回值填写下面json代码
>
> ```json
>    {
> 	    "random": "abcdefgh123456789",
> 	    "length": 9,
> 	    "key": "key",
> 	    "updateTime": 100,
> 	    "defaultValue": ""
>     }
> ```
>
> 上述json格式代码介绍：
> 	random：字符串，填写随机文本由哪些字符组成
>
> ​	length：整数，代表需要生成多长的随机文本
>
> ​	key：字符串，唯一识别码，可以随便填写，但是一个软件中用到多个随机返回值时需要填不一样的
>
> ​	updateTime：整数，代表着间隔多长时间更新一下随机文本，单位秒， -1代表每次都更新
>
> ​	defaultValue：非必填项

## 3.具体的hook模式

#### hook返回值

```java
//  例如1
  import simple.example;
  Class Example{
    public static boolean isFun() {
      boolean result = true;
      ...
       ...
      return result
    }
  }
  /*
  模式选择 Hook返回值
  类名应填：simple.example.Example
  方法名应填：isFun
  参数类型应填：（此处留空，因为没有参数）
  修改值应填：true 或者 false
  */

/*
多个参数参数类型的填法(用英语逗号分开，参数类型支持数组)：
boolean,int,android.content.Context
*/
// 例如2
  import simple.example;
  class Example{
    public static String isFun(Sring str, Context context, boolean b) {
      String result = str;
      ...
       ...
      return result
    }
  }
/*
  模式选择 Hook返回值
  类名应填：simple.example.Example
  方法名应填：isFun
  参数类型应填:
    java语法： java.lang.String,android.content.Context,boolean   (使用参数间使用英文逗号分开，仅一个参数不需要加逗号)
    smali语法：Ljava/lang/String;,Landroid/content/Context;,Z
  修改值应填：是个字符串 （应符合结果值的填写规则，不需要加引号）
*/
```

#### hook返回值+

>此功能可以将json转为对象(使用Gson)，你如果不知道这个对象的Json格式是什么样子的，可以使用【记录返回值】功能，复制返回值即可。这个功能并不是万能的，不适用所有情况，简单的数据类应该是没有问题的，暂不支持数组。
>
>模式：hook返回值+
>
>返回值的类名：填返回值的类名
>
>修改值：填json代码，如
>
>```json
>{"isHook":false,"level":10000}
>```
> 举个例子：
>
>```java
>import simple.example;
>
>// 数据类
>public class UserBean {
>    private boolean isHook;
>    private int level;
>
>    public UserBean(boolean isHook, int level) {
>        this.isHook = isHook;
>        this.level = level;
>    }
>}
>
>public class Example{
>    public static UserBean isFun() {
>      UserBean userBean = new UserBean(true, 10);
>      ...
>       ...
>      return userBean
>    }
>  }
>/*
>假如hook isFun的返回值
>模式：hook返回值+
>类名：simple.example.Example
>方法名：isFun
>参数类型：
>返回值的类名：simple.example.UserBean
>结果值：{"isHook":false,"level":10000}
>*/
>
>```
>
>

#### hook参数值

```java
// 类型值同 hook返回值类型
//特殊用法，如下面一段代码
public boolean isModuleLive(Context context, String str, int level){
  
    retrun true
}
//如果你只想要hook level的值，你可以在修改值那一栏向下面这样填
,,99
//如果你只想要hook str的值，你可以在修改值那一栏向下面这样填
,啦啦啦,
//如果你只想要hook str、level的值，你可以在修改值那一栏向下面这样填
,啦啦啦,99
//如果你想要全部hook，你可以在修改值那一栏向下面这样填
null,啦啦啦,99 // context为null也许导致闪退
/*
多个参数参数类型的填法(用英语逗号分开)：
android.content.Context,jave.lang.String,int
或者如下填写
Landroid/content/Context;,Ljave/lang/String;int
*/
```

#### 中断执行

```java
// 此模式会拦截方法执行
// 如hook返回值或者hook参数值一样填，不需要填写返回值、参数值
public void printString() {
    System.out.println("start");
    testBreakMethod();
    System.out.println("end");

    /*
      输出结果为
      start
      end

      test Break Mode 没有被输出
    */
}

// 假如:此方法被中断
public void testBreakMethod() {
    System.out.println("test Break Mode")
}
```

#### Hook所有同名方法

```java
/*
  Hook一个类所有同名方法，参数类型填写 * 即可
*/
```

#### Hook一个类中所有方法

```java
/*
  Hook一个类所有方法，方法名填写 * 即可；参数类型可随意填写，有些h不可为空
*/
```

#### 构造方法

```java
// 方法名填写：<init>
import simple.example;
public class Example{
  int a;
  int b;
  public Example(int a, boolean b) {
    this.a = a;
    this.b = b
  }
}
// Hook模式，根据自己的需求选择，一般为hook参数值/记录参数值，其他模式可能造成软件闪退
/*
  方法名填写：<init>
  例如修改两个参数值
    类名填写：simple.example.Example
    方法名填写： <init> 
    参数类型填写：int,int
    结果值填写：88,99
 */
```

#### HOOK静态变量

```java
import simple.example;
public class Example{
  public static boolean isTest = false;
}

import simple.example;
public class MainActivity extends Acitvity {
  @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initData();
        initView();
    }

    private void initData(){
      Example.isTest = false;
    }

    private void initView() {
      //你想要修改 isTest为true,所以你应当再这个变量被赋值后再去hook
      System.out.println(Example.isTest); 
    }
}
// 具体的值只支持基本类型，和字符串
// 无需填写变量类型；要符合[结果值]填写规则
/*
  模式选择 Hook静态变量
  hook点：after/before  根据需要填写，默认after
  类名应填：simple.example.MainActivity;
  方法名应填: initData
  参数类型应填：（什么都是不填，因为这个方法没有参数）
  变量所在类名：simple.example.Example
  变量名应填：isTest
  修改值应填：true/false
*/
```

#### HOOK实例变量

```java
import simple.example;
public class UseBean {
    private boolean isHook;
    private int level;

    public UseBean(boolean isHook, int level) {
        this.isHook = isHook;
        this.level = level;
    }

    public boolean isHook() {
        return isHook;
    }

    public void setHook(boolean hook) {
        isHook = hook;
    }

    public int getLevel() {
        return level;
    }

    public void setLevel(int level) {
        this.level = level;
    }
}

import simple.example;
public class MainActivity extends Acitvity {
  private User user;
  @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initData();
        initView();
    }

    private void initData(){
      user = new User(true, 100);
    }

    private void initView() {
      //你想要修改isHook、level,所以你应当再这个变量被赋值后再去hook
      System.out.println(user.isHook()); 
      System.out.println(user.getLevel()); 
    }
}
// 具体的值只支持基本类型，和字符串
// 无需填写变量类型；要符合[结果值]填写规则
/*
  模式选择 Hook变量
  hook点：after/before  根据需要填写，默认是after
  类名应填：simple.example.UseBean;
  方法名应填: <init>   // <init>  表示构造方法
  参数类型应填：boolean,int
  变量名应填：isHook
  修改值应填：true/false

  实例变量/成员变量：不支持像静态变量一样跨类hook，只能在本类的某个方法执行后，再去hook变量值
*/
```

#### 记录参数值

> 方法的参数值会被记录，前往记录页面可以查看、
> 若参数是数组或者list会被转成json格式

#### 记录返回值

> 方法的返回值会被记录，前往记录页面可以查看
> 若结果是数组或者list会被转成json格式

#### 记录参返

> 方法的参数值、返回值会被一同记录，前往记录页面可以查看
> 若结果是数组或者list会被转成json格式
> 若参数是数组或者list会被转成json格式

#### 扩展Hook

> 切记**打开总开关**
> 功能请前往app查看

## 4.常见问题(FAQ)

### 1.hook没有效果

> - 可看框架日志，是否有报错等
> - 储存文件更新配置某些情况下需要手动刷新，开启、关闭、编辑保存即可刷新
> - 请授予所需权限（android11以下：储存权限，android11及以上：ROOT权限）


### 2.什么是smali转配置

>  开启此实验功能后，配置页面顶部会增加‘粘贴板’图标，点击可将应用调用代码或签名，转化为配置（防止手动输入错误），增加配置后你需要手动选择合适的模式以及结果值
>   调用代码例子：
>
> ```smali
>  iget v0, p0, Lme/duck/hooktest/bean/UseBean;->level:I
>  invoke-virtual {v0}, Lme/duck/hooktest/bean/UseBean;->isHook()Z
> ```
>
>   方法签名、字段签名例子：
>
> ```smali
> Lme/duck/hooktest/bean/UseBean;->level:I
> Lme/duck/hooktest/bean/UseBean;->isHook()Z
> ```
>
> 上述可在MT管理器导航中长按字段或方法选择**复制签名**或者**查找调用**

### 3.为什么目标应用运行很慢

> 请关闭不必要的扩展HOOK和记录参数、返回值功能, 例如：md5、base64等，这些功能会产生大量的Log

### 4.

### 5.

### 6.什么是hook点

> hook静态变量、实例变量支持手动填写hook点，hook点就是在方法执行前hook还是在方法执行后hook
>
> before：方法执行前hook;
> after：方法执行后hook

### 7.什么是删除遗留配置

> 当你卸载本应用或者清除数据时，目标应用配置文件仍然可能保存在储存文件中
>
> 1. /data/local/tmp/simpleHook/目标应用包名/config/
> 2. /storage/emluated/0/Android/data/目标应用包名/simpleHook/config/ 
>
> 这个功能就是遍历所有的应用目录并删除无用的配置(本应用内未显示其配置)
>
> 因为需要遍历所有应用会比较慢
