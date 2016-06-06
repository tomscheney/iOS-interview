iOS 面试题


1.hitTest重写会有什么问题。
首先调用当前视图的pointInside:withEvent:方法判断触摸点是否在当前视图内；
若返回NO,则hitTest:withEvent:返回nil;
若返回YES,则向当前视图的所有子视图(subviews)发送hitTest:withEvent:消息，所有子视图的遍历顺序是从top到bottom，即从subviews数组的末尾向前遍历,直到有子视图返回非空对象或者全部子视图遍历完毕；
若第一次有子视图返回非空对象,则hitTest:withEvent:方法返回此对象，处理结束；
如所有子视图都返回非空，则hitTest:withEvent:方法返回自身(self)。
hitTest:withEvent:方法忽略隐藏(hidden=YES)的视图,禁止用户操作(userInteractionEnabled=YES)的视图，以及alpha级别小于0.01(alpha<0.01)的视图。
2.block在怎么避免强引用循环。
a.MRC
__block typeof(self) weakSelf = self;

b.ARC
__weak typeof(self) weakSelf = self;

静态变量 和 全局变量   在加和不加  __block 都会直接引用变量地址。也就意味着 可以修改变量的值。在没有加__block 参数的情况下。
全局block 和 栈block 区别为 是否引用了外部变量，堆block 则是对栈block  copy 得来。对全局block copy 不会有任何作用,返回的依然是全局block。
Block的copy、retain、release操作
不同于NSObjet的copy、retain、release操作：
	•	Block_copy与copy等效，Block_release与release等效；
	•	对Block不管是retain、copy、release都不会改变引用计数retainCount，retainCount始终是1；
	•	NSGlobalBlock：retain、copy、release操作都无效；
	•	NSStackBlock：retain、release操作无效，必须注意的是，NSStackBlock在函数返回后，Block内存将被回收。即使retain也没用。容易犯的错误是[[mutableAarry addObject:stackBlock]，在函数出栈后，从mutableAarry中取到的stackBlock已经被回收，变成了野指针。正确的做法是先将stackBlock copy到堆上，然后加入数组：[mutableAarry addObject:[[stackBlock copy] autorelease]]。支持copy，copy之后生成新的NSMallocBlock类型对象。
	•	NSMallocBlock支持retain、release，虽然retainCount始终是1，但内存管理器中仍然会增加、减少计数。copy之后不会生成新的对象，只是增加了一次引用，类似retain；
	•	尽量不要对Block使用retain操作，使用copy。






3.响应链有几种形式
a、触屏事件（Touch Event）
b、运动事件（Motion Event）
c、远端控制事件（Remote-Control Event）

iOS系统检测到手指触摸(Touch)操作时会将其放入当前活动Application的事件队列，UIApplication会从事件队列中取出触摸事件并传递给key window(当前接收用户事件的窗口)处理,window对象首先会使用hitTest:withEvent:方法寻找此次Touch操作初始点所在的视图(View),即需要将触摸事件传递给其处理的视图,称之为hit-test view。

window对象会在首先在view hierarchy的顶级view上调用hitTest:withEvent:，此方法会在视图层级结构中的每个视图上调用pointInside:withEvent:,如果pointInside:withEvent:返回YES,则继续逐级调用，直到找到touch操作发生的位置，这个视图也就是hit-test view。

4.SEL是个什么东西。
答：SEL 一个数据类型，定义是 char＊，每个方法名对应一个唯一的int值。selector的类型是SEL。SEL就是对方法的一种包装。包装的SEL类型数据它对应相应的方法地址，找到方法地址就可以调用方法。在内存中每个类的方法都存储在类对象中，每个方法都有一个与之对应的SEL类型的数据，根据一个SEL数据就可以找到对应的方法地址，进而调用方法。
SEL对象的创建。
SEL – 选择器的数据类型（typedef）；这种数据类型代表运行时的一种签名方法。它的否定词汇为 NULL。

	1	SEL s1 = @selector(test1); // 将test1方法包装成SEL对象　 
	2	SEL s2 = NSSelectorFromString(@"test1"); // 将一个字符串方法转换成为SEL对象 
由于整数的查找和匹配比字符串要快得多，所以这样可以在某种程度上提高执行的效率。

5.id和object,id<NSObject>，class 的区别。

（1）.id类型是一个Objective-C对象，但并不是都指向继承自NSOjbect的对象。它就是一个指针（isa）。动态对象类型。动态类型和静态类型对象的否定词汇为 nil。

（2）NSObject*就是 NSObject类型的指针了，它范围较小。

（3）id<NSObject>是指针，它要求它指向的类型要实现NSObject protocol，
iOS中很多类定义很奇葩，类名叫NSObject,定义个接口也叫NSObject，但是它俩不是一个东东。而NSObject类实现了NSOject接口，所以id<NSObject>可以指向NSObject的对象
id<NSObject>告诉编译器，你不关心对象是什么类型，但它必须遵守NSObject协议(protocol)，编译器就能保证所有赋值给id<NSObject>类型的对象都遵守NSObject协议(protocol)。这样的指针可以指向任何NSObject对象

 如果你不需要任何的类型检查，使用id，它经常作为返回类型，也经常用于申明代理(delegate)类型。因为代理类型通常在运行时，才会检查是否实现了那些方法。

（4）Class – 动态类的类型。它的否定词汇为 Nil。

6.object-c和swift的区别。
1）swift 编译的时候其实是转化为 oc ，oc再转化为c，c转化为汇编语言，汇编语言再转化为机器码
2）swift 语言更简洁，方法名和定义属性、变量更简单。
3）swift 吸取了Java、Javascript、C#的一些语言特点。
4）swift版本迭代频繁，oc目前没有版本迭代，很稳定，在未来几年内oc是不会被swift取代的；但是不排除oc被取代的可能性，比如iOS5强制使用ARC取代了MRC。

7.runloop是什么？
线程池，在iOS5之前可以创建线程池。
iOS5之后也可以使用线程池，指定线程池模型
可以用[NSRunLoop currentLoop]得到当前线程，设置当前线程。
NSDefalutRunLoopMode      默认状态.空闲状态
UITrackingRunLoopMode     滑动ScrollView
UIInitializationRunLoopMode    私有,App启动时
NSRunLoopCommonModes     默认包括上面第一和第二

利用 RunLoop 可实现自动释放池、延迟回调、触摸事件、屏幕刷新等功能。
model 主要是用来指定事件在运行循环中的优先级的.

8.线程之间怎么通信的？
1）GCD 
2) performselector
3) NSOperation

9.__block 和 __weak的区别
1.__block不管是ARC还是MRC模式下都可以使用，可以修饰对象，还可以修饰基本数据类型。 
2.__weak只能在ARC模式下使用，只能修饰对象（NSString），不能修饰基本数据类型（int）。 
3.__block对象可以在block中被重新赋值，__weak不可以。 
4.__block对象在ARC下可能会导致循环引用，非ARC下会避免循环引用，
5.__weak只在ARC下使用，可以避免循环引用。
10.缓存遗迹每个app运行分配的内存。

11.在扩展（extension）中声明的私有方法，其他类可以调用吗，强行调用会发生什么？

12.重载，重写，覆盖区别及联系。
重载：方法名相同，参数不相同。
重写：方法名，参数名，返回值都相同。覆盖和覆写都是重写。

13.extension和category
extension 只能添加属性，category为已有类添加方法
Category的使用场景：
1、当你在定义类的时候，在某些情况下（例如需求变更），你可能想要为其中的某个或几个类中添加方法。
2、一个类中包含了许多不同的方法需要实现，而这些方法需要不同团队的成员实现
3、当你在使用基础类库中的类时，你可能希望这些类实现一些你需要的方法。
 
遇到以上这些需求，Category可以帮助你解决问题。当然，使用Category也有些问题需要注意，
1、Category可以访问原始类的实例变量，但不能添加变量，如果想添加变量，可以考虑通过继承创建子类。
2、Category可以重载原始类的方法，但不推荐这么做，这么做的后果是你再也不能访问原来的方法。如果确实要重载，正确的选择是创建子类。
3、和普通接口有所区别的是，在分类的实现文件中可以不必实现所有声明的方法，只要你不去调用它

14.自动布局，怎么在代码里面实现动画。
通过修改autolayout来实现动画效果。

15.object－c中nil，null， NULL，［NSNull null］的区别。

16.scrollview及其子类在storyboard布局是contentsize问题的解决方案。 

17.单例有多少种实现形式，分别怎么实现的。

18.写一个简单的算法，比如二分法，冒泡，链表查询，
 
19.视频流的编码解码.

20.define的使用.

21.swift的优势

22.oc声明block为什么要用copy

23.coredata的用法

24.runloop的几种类型，使用场景,和线程的关系。

25.iOS缓存机制
 1）coreData,FMDB
 2)  NSUserDefaults
 3)  SQLite
 4)  file(plist/txt/)


26.gcd实现单例

27.runtime创建类

28.keypath在storyboard实现view圆角.

29.jsonkit的内部实现

30.main函数之前，写main函数.
1）.动态链接库
系统使用动态链接有几点好处：
	•	代码共用：很多程序都动态链接了这些lib，但它们在内存和磁盘中中只有一份
	•	易于维护：由于被依赖的lib是程序执行时才link的，所以这些lib很容易做更新，比如libSystem.dylib是libSystem.B.dylib的替身，哪天想升级直接换成libSystem.C.dylib然后再替换替身就行了
	•	减少可执行文件体积：相比静态链接，可执行文件的体积要小很多
2）.dyld - the dynamic link editor
	1	从kernel留下的原始调用栈引导和启动自己
	2	将程序依赖的动态链接库递归加载进内存，当然这里有缓存机制
	3	non-lazy符号立即link到可执行文件，lazy的存表里
	4	Runs static initializers for the executable
	5	找到可执行文件的main函数，准备参数并调用
	6	程序执行中负责绑定lazy符号、提供runtime dynamic loading services、提供调试器接口
	7	程序main函数return后执行static terminator
	8	某些场景下main函数结束后调libSystem的_exit函数

一切源于dyldStartup.s这个文件，其中用汇编实现了名为__dyld_start的方法，汇编太生涩，它主要干了两件事：
	1	调用dyldbootstrap::start()方法（省去参数）
	2	上个方法返回了main函数地址，填入参数并调用main函数

_objc_init 这个函数是runtime的初始化函数。

3）ImageLoader
当然这个image不是图片的意思，它大概表示一个二进制文件（可执行文件或so文件），里面是被编译过的符号、代码等，所以ImageLoader作用是将这些文件加载进内存，且每一个文件对应一个ImageLoader实例来负责加载。
两步走：
	1	在程序运行时它先将动态链接的image递归加载 （也就是上面测试栈中一串的递归调用的时刻）
	2	再从可执行文件image递归加载所有符号
当然所有这些都发生在我们真正的main函数执行前。

4）runtime与+load
刚才讲到libSystem是若干个系统lib的集合，所以它只是一个容器lib而已，而且它也是开源的，里面实质上就一个文件，init.c，细节不说了，由libSystem_initializer逐步调用到了_objc_init，这里就是objc和runtime的初始化入口。

31.kvo内部实现

32.元组和数组的区别

33.人脸识别iOS有类实现？

34.autorelease 的原理，有一个4k的文件存的是对象地址

35.研究AFNetWorking。CFNetwork,最新MKNetworkKit.

36.研究操作队列，多线程，GCD。

37.动画实现的几种方式，

38.研究block。

39.kvc(键值对编码)，kvo（键值对观察）

40.iOS应用内支付

41.NSSet和NSArray的区别

42.coredata研究一下，

43.图文混排研究一下

44.算法看一下！

45.做一个apple wacth的 demo

46.研究label自适应文本。

47.copy，asign,weak,strong,retain内存管理

copy关键字只能应用于对象，不能用于基本类型
对于字符串，理应始终使用copy，虽然使用strong一般情况下也没有关系
对于不可变集合类型，有可变和不可变类型，若要防止外部的修改影响所传过来的值，应该使用copy来声明，虽然大多情况下使用strong一定问题都没有。不过，实际开发中，我见到的几乎都是使用strong来声明的，包括笔者在内。
对于可变集合类型，都应该使用strong来声明，不能使用copy，因为copy会生成一个不可变的类型，而不是可变的。
对于block，都应该使用copy来声明，原因是block来捕获上下文的信息。

assign 可以修饰对象类型，也可以修饰基本数据类型，修饰的对象在不用时要置为nil。

weak 只可以修饰对象类型，只在ARC下使用，避免强引用循环

48、@property的本质是什么？ivar、getter、setter是如何生成并添加到这个类中的？
@property = ivar（实例变量） + getter（取方法） + setter（存方法）

49、@protocol和category中如何使用 @property
参考答案：
在protocol中使用@property只会生成setter和getter方法声明，我们使用属性的目的是希望遵守我协议的对象能实现该属性
category使用@property也是只会生成setter和getter方法的声明，如果我们真的需要给category增加属性的实现，需要借助于运行时的两个函数：
objc_setAssociatedObject
objc_getAssociatedObject

50. BAD_ACCESS在什么情况下出现？

访问了野指针，比如对一个已经释放的对象执行了release、访问已经释放对象的成员变量或者发消息。 死循环也会出现该提示。

同步会阻塞当前线程，执行完block里面任务才会继续往下走。dispatch_sync阻塞了主线程，然后把任务追到加主队列，并在主线程执行，但是此时的主线程已经被阻塞，所以block任务也无法执行，block任务不执行，dispatch_sync会阻塞还主线程。这样子就产生了死锁。

总结起来：不要使用sync（同步）向串行队列添加任务，否则会产生死锁。

51如何让自己的类用 copy 修饰符？如何重写带 copy 关键字的 setter？
若想令自己所写的对象具有拷贝功能，则需实现 NSCopying 协议。如果自定义的对象分为可变版本与不可变版本，那么就要同时实现 NSCopying 与 NSMutableCopying 协议。
1)需声明该类遵从 NSCopying 协议
2)实现 NSCopying 协议。该协议只有一个方法:
(id)copyWithZone:(NSZone *)zone;

52、ARC下不显式指定任何属性关键字时，默认的关键字都有哪些？
参考答案：
对于基本数据类型默认关键字是：atomic,readwrite,assign
对于普通的Objective-C对象：atomic,readwrite,strong


53.framework 和lib的区别。

54.并发线程和串发线程。





人脸识别技术主要包括
1.人脸检测 2.特征提取 3.人脸确认

关于人脸识别的算法
但是由于研究者学术背景和应用的不同算法不尽相同，主要有（主动形状模型）和（主动表观模型）是两个非常那个有效的特征提取算法，他们都是基于统计的参数化可变模型。

一般来说，人脸识别系统包括图像摄取、人脸定位、图像预处理、以及人脸识别（身份确认或者身份查找）。系统输入一般是一张或者一系列含有未确定身份的人脸图像，以及人脸数据库中的若干已知身份的人脸图象或者相应的编码，而其输出则是一系列相似度得分，表明待识别的人脸的身份。
人脸识别算法分类
基于人脸特征点的识别算法（Feature-based recognition algorithms）。
基于整幅人脸图像的识别算法（Appearance-based recognition algorithms）。
基于模板的识别算法（Template-based recognition algorithms）。
利用神经网络进行识别的算法（Recognition algorithms using neural network）。

基于光照估计模型理论
提出了基于Gamma灰度矫正的光照预处理方法,并且在光照估计模型的基础上，进行相应的光照补偿和光照平衡策略。
优化的形变统计校正理论
基于统计形变的校正理论，优化人脸姿态；强化迭代理论
强化迭代理论是对DLFA人脸检测算法的有效扩展；
独创的实时特征识别理论
该理论侧重于人脸实时数据的中间值处理，从而可以在识别速率和识别效能之间，达到最佳的匹配效果

技术流程：
人脸图像采集及检测、人脸图像预处理、人脸图像特征提取以及匹配与识别。


视频直播
HTTP Live Streaming（HLS）是苹果公司(Apple Inc.)实现的基于HTTP的流媒体传输协议，可实现流媒体的直播和点播，主要应用在iOS系统
根据以上的了解要实现HTTP Live Streaming直播，需要研究并实现以下技术关键点
	1	采集视频源和音频源的数据
	2	对原始数据进行H264编码和AAC编码
	3	视频和音频数据封装为MPEG-TS包
	4	HLS分段生成策略及m3u8索引文件
	5	HTTP传输协议




直播工作流模型
	1	Client (iOS/Android/PC/Camera) 向 Server (业务逻辑服务器) 请求推流授权
	•	Server 颁发带授权信息的 Stream 给 Client 
	•	Client 通过 RTMP 推流 给 Pili Streaming Cloud
	•	Client 向 Server 请求播放授权
	•	Server 向 Client 颁发播放地址
	•	Client 调用 播放器 SDK 打开播放地址进行播放
