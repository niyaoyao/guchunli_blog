---
title: iOS面试实战总结
date: 2017-06-30 14:31:59
categories: 笔记
tags: [面试]
---

# youxin
1.阐述一下 runtime,runloop，并举几个应用场景。
runtime:动态创建类和对象，进行消息发送和转发
runloop运行循环:
一.保持程序的持续运行。
二.处理App中的各类事件（触摸事件、定时器事件、Selector事件）
三.节省CPU资源，提高程序性能：没有事件时就进行睡眠状态
内部实现：do-while循环。

2.请列举你知道的设计模式。

3.请设计一个账号系统，需考虑持久化，登录态等。

4.请用代码或者伪代码打印输出一个菱形。
```
void print(int n)
{
    int i,j;
    for(i=1; i<=n; i++)
    {
        for(j=1; j<=n-i; j++){ printf(" "); }
        for(j=n-i+1; j<n+i; j++){ printf("*"); }
        printf("\n");
    }

    for(i=n-1; i>=1; i--)
    {
        for(j=1; j<=(n-i); j++){ printf(" "); }
        for(j=n-i+1; j<n+i; j++){ printf("*"); }
        printf("\n");
    }
}
```

5.sdwebimage使用到了哪些缓存机制，缓存到什么路径下。
Memory 和 Disk 双重 Cache 机制。缓存到tmp的cache文件夹下，NSCachesDirectory。

6.是否使用或了解过swift，react native，优缺点是什么。

7.线上crash如何解决。
bug严重性分析：确定紧急版本更新，迭代版本中修复，热修复。
（1）如果是服务端返回的异常数据没做兼容，就由服务端确保格式正确，客户端看情况是否要做兼容；
（2）如果是升级版本后的由于本地数据库或本地存储的数据格式未兼容等问题，一般需要撤下版本重新提交；
（3）已经发布的版本集成过热修复模块，否则也要重新发布版本。

8.请写出你知道的排序算法及其复杂度。
（1）冒泡排序
（2）插入排序
（3）选择排序
（4）快速排序
![排序算法比较](assets/demo/sort.png)
![排序算法图解](assets/demo/sort_desc.png)

9.AutoreleasePool是如何实现的。
* AutoreleasePool（自动释放池）：是OC中的一种内存自动回收机制，它可以延迟加入AutoreleasePool中的变量release的时机。
* AutoreleasePool与线程：每一个线程都会维护自己的 autoreleasepool 堆栈，每一个 autoreleasepool 只对应一个线程。Autorelease对象是在当前的runloop迭代结束时释放的。
* autorelease何时释放：在没有使用@autoreleasepool的情况,autorelease对象是在当前的runloop迭代结束时释放。每个runloop中都会创建一个 autoreleasepool 并在runloop迭代结束进行释放。如果是手动创建autoreleasepool,自己创建Pool并释放
* AutoreleasePool是如何实现的：通过声明一个__AtAutoreleasePool类型的局部变量__autoreleasepool实现了@autoreleasepool{}。一个线程的autoreleasepool就是一个指针栈，每个runloop迭代中都加入了自动释放池Push和Pop。

10.ipa体积如何压缩。
（1）配置编译选项 （Levels选项内）Genetate Debug Symbols  设置为NO，这个配置选项应该会让你减去小半的体积。
（2）舍弃架构armv7，因为armv7用于支持4s和3gs
（3）编译的版本必须是发布版本
（4）资源：
1）图片：去掉无用的图片；用代码绘制简单的纯色图片；一些对图片要求高的用位图，其他的尽量用矢量图；如果不需要使用透明，可以用jpeg代替PNG
2）音视频：不增加编解码的前提下正确选择格式
3）文件：检查bundle中的无用文件，不要打包到app或者静态库中。可以点击文件，在右侧的file inspector里面的target membership中取消勾选；或者在build phase里面的Copy Bundle Resources中去掉。
4）代码：确定 dead code（代码被定义但从未被调用）被剥离，build setting 里 DEAD_CODE_STRIPPING = YES。 去掉冗余的代码，即使一点冗余代码，编译后体积也是很可观的。
5）第三方库：查找内部使用到的第三方库，一方面可以进行删减代码，用不到的类，直接删除，还有第三方库中的图片资源统统删除掉，如果能够自己手写实现的，那费功夫自己写吧
注意：把product 里面的文件 使用“显示包内容”方式，按照文件大小排序，查看打完包后具体是哪些图片占用太多的空间，并针对性地优化

## http网络请求过程

## 对称加密与非对称加密
### 对称加密
对称加密：加密（encryption）与解密（decryption）使用的是同样的密钥（secret key）。加密和解密算法是公开的，秘钥必须严格保存，如果秘钥泄露，别人就能够用密文+秘钥还原成你的明文。
优点：算法公开、计算量小、加密速度快、加密效率高。
缺点：秘钥的管理和分发非常困难，不够安全。
比如：常见的DES/AES都是属于对称加密算法。

### 非对称加密
非对称加密：需要两个密钥来进行加密和解密，这两个秘钥是公开密钥（简称公钥）和私有密钥（简称私钥），即常说的“公钥加密，私钥加密”或“私钥加密，公钥加密”。私钥只能由一方安全保管，不能外泄，而公钥则可以发给任何请求它的人。非对称加密使用这对密钥中的一个进行加密，而解密则需要另一个密钥。比如，你向银行请求公钥，银行将公钥发给你，你使用公钥对消息加密，那么只有私钥的持有人--银行才能对你的消息解密。与对称加密不同的是，银行不需要将私钥通过网络发送出去，因此安全性大大提高。
优点：安全性更高，公钥是公开的，秘钥是自己保存的，不需要将私钥给别人。
缺点：加密和解密花费时间长、速度慢，只适合对少量数据进行加密。
比如：RSA就是最常用的非对称加密算法。

# momo
## 1.NSString *name，非ARC下重写setter,getter方法
```
-(void)setName:(NSString *)name{
    if (_name != name) {
        [_name release];
        _name = [name copy];
    }
}
- (NSString *)name{
    return [[_name retain]autorelease];
}
```

<!--## 2.简述OC的内存管理模式，包括alloc,retain,copy,release,autorelease,dealloc这些方法的理解。-->

## 3.描述autorelease对象释放时机，简述autorelasepool的工作机制


## 4.在OC中如何判定两个对象完全相等
[obj1 isEqual:obj2]

## 5.以下作为button的响应方法会有什么问题，能看到log吗
- (void)clickButton{
    dispatch_sync(dispatch_get_main_queue(), ^{
    NSLog("log");
    });
}
线程阻塞，不能，主线程不能使用同步方法

## 6.在一个类中retain一个NSTimer类型的成员变量会有问题吗，为什么
有问题，会retain他的target，target指定为self，会循环引用

## 7.简述C的函数调用和OC的消息传递的机制区别
C函数调用顺序执行
OC消息传递，异步执行

## 8.OC中category的实现原理和runtime有关吗，什么联系
有关，再对下的编译之后，运行期动态添加方法。

## 9.NSOperationQueue和GCD有什么区别，实现机制有什么不同
GCD更简洁，底层C语言实现。

## 10.列举在iOS开发中擅长的模块和其特点使用时的注意问题（例如：音频录制和播放，mapkit定位等）
mapkit：always/wheninuse background

## 11.简述1.2个自己经常使用的第三方库和其优缺点

<!--## atomic／lock-->

## 使用一种数据结构替代NSNotificationCenter
dictionary

## NSSet使用场景

# zhihu
## 1.rebase

## 2.私有库

## 4.NSURL,NSURLSession,NSURLConnection,NSURL


# xiaoyezi

## wkwebview交互过程造成内存泄漏

## Safari 怎么调试加载的h5，怎么控制手机页面调试
* 1.Safari设置-safari偏好设置-高级菜单-在菜单中显示“开发”菜单，选中复选框，这样就能看到开发菜单了
* 2.iPhone设置-safari-高级-打开“javascript”开关和web检查器开关
* 3.iPhone接上Mac，运行手机app里的web页面，在开发菜单中选择连接的手机，找到调试的网页，就能在Safari里面调试了，可以打断点，查看cookie，打印cookie或元素，添加alert

## IM消息状态提醒，比如正在输入之类的

## n个台阶，一次走一步或者两步，有多少种走法

## git回退

## view从创建到展示出来都会经历哪些过程
loadView创建：
(1)[super loadView]
    1)指定了与控制器相关联的xib文件，通过xib文件创建控制器的view
    2)没有明显地传xib文件名，加载与控制器同名的xib文件
    3)没有找到相关联的xib文件，创建一个空白的UIView，赋值给控制器的view
(2)在loadView方法中，通过代码创建view，不需要调用[super loadView]
过程：loadView->viewDidLoad->viewWillAppear->viewWillLayoutSubviews->viewDidLayoutSubviews->viewDidAppear->viewWillDisappear->viewDidDisappear->viewDidUnLoad

# dongao
<!--1.strong，retain，copy，assign，weak，unsafe_unretained-->
2.远程推送
<!--3.copy，nsmutablearray-->
4.layoutsubviews什么时候调用
5.内存空间
6.内存优化
7.创建一个对象过程
8.define，const区别
9.五张图片合成一张，异步下载
10.OC动态语言理解

# 高德
<!--* reatin copy 属性 代码-->

* 通知 主/子线程

* setValue/setObject   forKey/forKeyPath 字典 

* CGRect id NSValue

* ABC排序

* nil、Nil、NULL、NSNull  
nil：定义一个实例为空，指向一个OC对象的空指针，而且我们对于nil的调用方法，是不会产生crash或者抛出异常。
NULL：nil是一个对象，而NULL只是一个空值而已。
Nil：定义一个空的类，指向一个类的空指针。
NSNull：通常标识集合中的空值，定义了一个单例对象用于表示集合对象的空值。集合对象无法包含nil 用来作为其具体的值，譬如NSArray.NSSet和NSDictionary.相应的，nil值用一个特定的对象NSNulll来表示。NSNull只是提供了一个单一实例用力表示对象属性中的nil值。在默认的实现方法中，dictionaryWithValuesForKeys:和setValuesForKeysWith:自动地奖NSNull和nil相互转换，因此我们设置的对象不需要进行NSNulld的测试操作。
```
NSURL *url = nil;
Class cls = Nil;
int *pointerInt = NULL;
NSArray *arr = [NSArray arrayWithObjects:[[NSObject alloc]init],[NSNull null],[[NSObject alloc]init],[[NSObject alloc]init],nil,nil];
NSLog(@"%@--%ld",arr,arr.count);
//打印结果
(
"<NSObject: 0x600001de85c0>",
"<null>",
"<NSObject: 0x600001de8570>",
"<NSObject: 0x600001de85f0>"
)--4
```

* uibutton hitTest

* GCD

* TableView优化

* block 3种

* tableView 实现滚动完成加载数据

* msgSend 消息发送

* runloop timer

* OC 高效

# sougou
第一部分：选择题（有多选）
1.在OC中类接口声明的关键字为：（ D ）
A、@import
B、@interface
C、@implementation
D、@protocal
2.不会立刻使引用计数器改变的是（ C ）
A、release
B、alloc
C、autorelease
D、retain
3.下属代码打印的结果是：（ C ）
NSString *name = [[NSString alloc]initWithString:@"张三"];
NSLog(@"%ld",[name retainCount]);
A、-1
B、0
C、1
D、2
4.控制台打印的最后一条信息会是：（ A ）
NSDictionary *dict = [NSDictionary dictionaryWithObject:@"v" forKey:@"k"];
NSLog(@"%@",[dict objectForKey:@"k"]);
[dict release];
A、v
B、k:v
C、崩溃信息
D、v:k
5.以下说法正确的是：（ B ）
A、nonatomic：提供多线程保护，提高性能，在没有使用多线程的编程中可以选择使用
B、assign：属性默认值，说明设置器直接进行赋值，针对基础数据类型（NSInteger,CGFloat）和C数据类型（int,float,double,char）等。
C、retain：此属性只用于obj-c的对象类型，对参数进行release 旧值，再retain 新值。
D、copy：此属性只对实现NSCoping 协议的对象有效（NSString）,拷贝工作由copy方法执行
6.下面代码对字符串str进行拼接后，2次打印的地址是否相同：（ A ）
NSMutableString *str = [[NSMutableString alloc]initWithFormat:@"123"];
NSLog(@"str = %p",str);
[str appendFormat:@"456"];
NSLog(@"str = %p",str);
A、相同
B、不同
C、随机打印
D、有时相同，有时不同
7.以下说法正确的是：（ B ）
A、OC不用"函数调用"，而用互相传递讯息
B、OC一个完整的类分为interface和implemention两块
C、OC对象使用new分配内存，用delete释放
D、OC协议分为非正式协议和正式协议
8.OC中包含一个自定义的头文件应该使用：（ C ）
A、#include ""
B、#include <>
C、#import ""
D、import <>
9.OC中的委托最好声明成：（ B ）
A、retain
B、assign
C、copy
D、readonly
10.创建对象时，对象的内存和指向对象的指针分别分配在哪里：（ A ）
A、堆区，栈区
B、常量区，堆区
C、全局区，栈区
D、栈区，堆区
11.关于KVC的描述错误的是:（ A ）
A、KVC 是指 "Key-Value Observing"
B、是一种间接访问对象属性的机制
C、只能访问对象的属性
D、当对象的属性值改变时，我们能收到一个通知
12.以下说法正确的是:（ C ）
A、求数组的内容的个数用length方法
B、字典时根据其位置来索引数据的
C、协议中定义的方法默认是必须实现的
D、定义类目必须要拿到自己类的源代码
13.内存管理的关键字描述错误的是:（ D ）
A、retainCount是指对象的引用计数
B、retain关键字可以增大对象的引用计数
C、release可以减小对象的引用计数，但autorelease不可以
D、dealloc方法不能直接调用
14.下面描述正确的选项是:（ B ）
A、当计数器为1时，dealloc方法由系统自动调用
B、一个对象在引用计数变为0时，会调用自己的dealloc
C、在dealloc方法里，应该在[super dealloc]后释放本类所拥有的对象
D、开启ARC后，可以重载dealloc，必须在dealloc里写[super dealloc]方法
15.下面哪个选项是正确的（ B ）
A、用继承可以扩展一个类，要调用扩展的方法，既可以用父类，也可以用子类
B、用类目可以扩展一个类，可以直接用该类或该类的实例对象，去调用该类目扩展的方法
C、延展就是类目
D、可以在一个类的外部，调用这个类延展的方法

第二部分：问答题（至少答四题）
1.在iOS中实现回调一般有哪几种方法
block,delegate
2.OC中实现多线程有哪几种方式
NSThread,pthread,NSOperation+NSOperationQueue,GCD
3.NSMutableDictionary 中的 setObject:forKey 和 setValue:forKey 有什么区别

4.weak 和 assign 有什么区别

5.initWithCoder 和 initWithFrame 有什么区别

6.NSView 的 frame、bounds 和 center 有什么区别
frame：相对于父视图
bounds：相对于自身
center：中心店
7.awakeFromNib,initWithCoder,viewDidLoad 的调用顺序和调用时间

第三部分：编程题
1.编程实现字符串拷贝（考虑内存重叠）
char *strcpy(char *src,char *dst);

2.利用分类给NSString 扩展三个方法：
（1）字符串翻转（比如：@"123"，调用方法后，返回@"321"）
（2）计算英文字母的个数（比如：@"123abc456"，调用方法后，返回@"3"）
（3）去除字符串两端的空格（比如：@"123 456"，调用方法后，返回@"123456"）
```
@interface NSString(LPY)

+ (NSString *)stringWithReverse:(NSString *)rString;
- (NSString *)initWithReverse;

+ (NSString *)stringWithCharacterLength:(NSString *)lString;
- (NSString *)initWithCharacterLength;

+ (NSString *)stringWithTrim:(NSString *)tString;
- (NSString *)initWithTrim;

@END
```

# 未知
第一部分：swift技术
1.struct Tutorial{
var difficulty: Int = 1
}
var tutorial1 = Tutorial()
var tutorial2 = tutorial1
tutorial2.difficulty = 2
请说明：tutorial1.difficulty和tutoria2.difficulty 的值分别是多少
如果Tutorial是一个类会有什么不同，说明原因
1.2 2.2 结构体值拷贝，类指针拷贝
2.下面code是把数组按字母顺序进行排序，看起来有些复杂，你能用闭包代码简化它吗
```
let sortedAnimals = animals.sort{(one: String,two: String) -> Bool in
return one<two
}
```
sort{<} sort{$0<$1}
3.下面代码能否编译通过
public struct ThermometerStruct {
    private(set) var temperature: Double = 0.0
    public mutating func registerTemperature(temperature: Double){
        self.temperature = temperature
    }
}
let thermometerStruct = ThermometerStruct()
thermometerStruct.registerTemperature(56.0)
4.下面代码打印输出是什么？为什么？
```
var thing = "cars"
let closure = {(thing) in
print("I love \(thing)")
}
thing = "airplane"
```
I love airplane
5.下面代码会出什么错误，如何修复
struct Kitten{

}
func showKitten(kitten: Kitten?){
    guard let k = kitten else {
        print ("there is no kitten")
    }
    print(k)
}
6.什么是optional类型，它是用来解决什么问题的，对一个optional变量拆包有多少种方法
7.swift中枚举类型中，row values 和associated values有什么区别
8.下面代码中，x时什么类型，值是什么
let d = ["john":23,"james":24,"vincent":34,"louis":29]
let x = d.sort{$0.1<$1.1}.map{$0.0}

第二部分：iOS技术
1.view 类中，bounds和frame有什么区别
2.描述viewcontroller的生命周期事件顺序
3.描述iOS APP 应用程序声明周期状态
4.列举比较重要的application delegate methods
5.iOS中如何进行并发编程开发，请描述各种方式的使用方式和优缺点

第三部分：编程
1.给Array写一个扩展方法，查一下Array中不相同元素的个数（注意需要泛型类型支持，且类型遵循comparable协议），如：[1,2,3].countUniques() //输出结果：3

第四部分：逻辑题

# zaiyoudao
1.id 声明的属性有什么特性
2.@public,@protect,@private 它们的含义与作用
3.深复制和浅复制的区别
4.NSArray 和NSMutableArray 的区别
6.在iPhone应用中如何保存数据
7.view和view之间传值方式
8.请简单介绍下APNS发送系统消息的机制
9.__block和__weak 修饰符的区别
10.define和const常量的区别
19.描述下SDWebimage中给UIImageView加载图片的逻辑。

# buchang
1.iOS设计模式是什么，你知道哪些设计模式，并简要叙述
2.#import 和#include 有什么区别，@class呢，#import<>和#import()有什么区别
3.category（类别）、extension（扩展）和继承的区别
4.什么事KVC和KVO
5.ViewController生命周期
6.进程和线程的区别，同步异步的区别，并行和并发的区别
7.什么时候报 unrecognize selector 错误，iOS有哪些机制来避免走到这一步
8.runloop 是来做什么的，runloop 和线程有什么关系，主线程默认开启了runloop吗，子线程呢
9.类方法和实例方法有什么区别
10.isKindOfClass,isMemberOfClass selector 作用分别是什么
11.gGCD和NSOperation 的区别
12.Runtime 实现的机制是什么，怎么用，一般用于干嘛
13.什么是 method swizzling
14.假定输入的字符串中只包含字母和*号，编写函数fun，功能是：除了中间和尾部的*外，将字符串中其他*全部删除。编写时，不用C的其他函数。例：*****A*BC*DEF*G****，结果为：A*BC*DEF*G****

# feienlaisi 
1.NSObject 中 description 属性的意义，它可以重写吗
2.懒加载（延时加载）
3.UIView和CALayer的区别
4.优化UITablebView的性能（比如含有正在加载的网络图片不会卡）
5.cocoapods
7.进程间的通信方式
8.static 的作用
9.线程的状态
10.AFNetworking 的底层实现
11.nil,Nil,NULL,NSNull
12.简述 NotificationCenter,KVC,KVO,delegate，并说明它们之间的区别
13.drawRect 和 layoutsubviews 的区别
14.controllerA调用controllerB后，如何在B中对A进行一些代码操作，请用block和delegate分别实现

# yige
1.请写一个单例的实现
2.简单描述一个MVC,MVP,MVVM的优缺点
3.常用的多线程有哪几种，以及使用场景
4.简单写出VC的生命周期（执行的方法及顺序）
<!--5.@property的本质是什么，有哪些属性关键字和关键字的使用情况。-->

# fz apb
1.描述一个MVC模式和MVVM模式等具体应用场景和如何应用，以及两种模式的最根本区别

# zili（swift）
1.用var 声明view1和用let声明view2，在下面的例子中有什么不同，能否编译通过
```
import UIKit
var view1 = UIView()
view1.alpha = 0.5
let view2 = UIView()
view2.alpha = 0.5 //will this line compile
```
<!--3.属性关键字 readwrite，readionly，assign，retain，copy，nonatomic 各有什么作用，在哪种情况下用-->
4.你是否接触过OC中的反射机制，简单聊一下概念和使用
5.开发项目中你怎么检查内存泄露
6.下面的代码输出什么
```
@implementation Son: Father
- (id)init{
    if(self = [super init]){
    NSLog(@"%@",NSStringFromClass([self class]));//Son
    NSLog(@"%@",NSStringFromClass([super class]));//Son
    }
    return self;
}
```
答：结果self和super都是指向当前实例的。不同的是，[self class]会在当前类的方法列表中去找class这个方法，[super class]会直接开始在当前类的父类中去找calss这个方法，两者在找不到的时候，都会继续向祖先类查询class方法，最终到NSObject类。那么问题来了，由于我们在Father和Son中都没有去重写class这个方法，最终自然都会去执行NSObject中的class方法，结果也自然应该是一样的。至于为什么是Son，我们可以看看NSObject中class的实现：
```
-(Class)class { 
return object_getClass(self); 
}
```
这就说的通了，返回的都是self的类型，self此处正好就是Son，因此结果就会输出Son。
9.swift2.0引用了一个新关键字能产生递归枚举类型，下面是一个带有Node节点的枚举类型，Node关联值类型，T和List：
enum List<T>{
    case Node(T,List<T>)
}
10.swift中struct和class有什么区别，举个应用中的实例
class可以继承 值拷贝/指针拷贝
12.OC的类可以多重继承吗，可以实现多个接口吗，category 是什么，重写一个类的方式用继承好还是分类好，为什么
<!--13.什么情况使用weak关键字，相比assign有什么不同-->
14.设计模式是什么，你知道哪些设计模式，并简要叙述
15.MVC和MVVM的区别

# aigelasi
1.想让被引入的framework只在需要时才被载入内存，应该将framework的属性设置（optional）
2.info.plist里添加白名单时，涉及到的字段名：(LSApplicationQueriesSchemes)
3.声明一个类要用到的编译指令：@interface SomeClass
4.Navigaition controllers should be presented and ViewControllers should be pushed.
简答
1.HTTPS有关的ATS指的什么
2..cer .p12 .mobileprovision文件都是什么
3.iOS动态库与静态库区别
4.描述以下代码的含义
self.timer = [NSTimer scheduledTimer...];
5.用OC代码实现
（1）字符串拼接 （2）字符串比较 （3）将OC字符串转化为C字符串 （4）获取应用的cache目录 （5）打印一个bool值
6.[UIScreen mainScreen].bounds 与 [UIScreen mainScreen].applicationFrame 区别
7.responseToSelector什么时候用
8.-all-load含义
9.weak什么时候用
10.项目配置里：Achitectures、build Active Achitectures、valid Achitectures分别什么含义
11.bitcode时候enable，什么区别
12.CFBundleShortVersionString 与 CFBundleVersion ，build与version 如何对应
编程
1.用OC代码实现一个单例
2.写出一个简单的洗扑克牌的算法

# other
* 热修复
JSPatch ReactNative Lua+Wax

* 列举自己的优点和缺点


参考文章：[iOS之Safari调试webView/H5页面](https://www.cnblogs.com/dianming/p/6902442.html)
