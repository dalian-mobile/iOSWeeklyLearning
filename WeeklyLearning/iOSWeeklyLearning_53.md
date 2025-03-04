# iOS 摸鱼周报 #53 | 远程办公正在成为趋势

![](https://cdn.zhangferry.com/Images/moyu_weekly_cover.jpeg)

### 本期概要

> * 话题：远程工作正在成为趋势
> * 面试模块：CRC 实践应用：理论推导
> * 优秀博客：App Extension
> * 学习资料：一个大学生的学习计划
> * 开发工具：一款由开源mpv 提供支持，适用于 macOS 的现代全能媒体播放器 IINA。

## 本期话题

### 远程办公正在成为趋势

随着疫情的影响，远程工作的趋势正在以更快的速度发展和普及。携程、爱彼迎、GitLab 等都先后出台政策鼓励员工远程办公，可以预见不久之后还会有更多公司支持常态化的远程办公。远程工作是一个很早就有的概念，它也天然适合互联网行业，那为什么却发展这么慢呢，可以简要对比下传统办公模式和远程办公各自的优缺点。

**传统办公模式**

优点是同事之间沟通便利、工作状态更沉浸、公司整体也更有凝聚力；如果公司福利好的话，像管饭、健身等都在增加其优势。

传统办公最大缺点就是通勤了，特别是离家远的话，每天两遍的通勤简直磨炼人的。因为必须到岗，个人的自由度，作息习惯也相应的受公司制度影响。

**远程办公**

最大的优点是赋予了个人很大的自由性。不仅省去了通勤，还能更顾家，像接送小孩等，也能很方便的排开时间去处理。自由度是否意味着幸福感可能还要因人而异，但这种模式确实把选择权交到了个人手里。同时其对公司也有很大好处，会节省很多管理成本。

缺点的话，从我的观察是远程办公会导致大家最多抱怨的是不知道吃什么😄。

还有一方面是它对效率的影响。这段时间北京疫情一直不稳定，我断断续续已经居家办公有一个月时间了。实际感受来看，在家办公的效率平均下来可能是在公司的 80% - 90%。这效率损失主要在两个地方，一是在家被琐事打扰的概率要比公司更高，二是投入度的影响，公司的环境所带来的沉浸感（大屏显示器）在家较难实现（也会因人而异）。

也是因为这些影响，不是所有人都习惯远程办公的，但这是由于强制远程带来的问题，那如果是否远程交到员工自己手里呢？可以再结合采取了远程办公的公司采取的制度来看这个事情。

| 公司   | 远程制度                                                    |
| ------ | ----------------------------------------------------------- |
| 携程   | 「3 + 2」模式，每周三、周五在家远程办公，其余三天去公司上班 |
| 爱彼迎 | 永久灵活选择，可在家、在办公室，无论地点在哪，薪酬不变      |

携程代表混合模式，爱彼迎代表纯自由模式。爱彼迎比较彻底，永久灵活，但注意是灵活，个人还是有权力回归办公室的。携程的混合模式更像是纯远程和传统的中间态，既提供了一定的自由度，也有约束条件定期回归公司以维系归属感。携程这个是更偏探索的一种状态，感觉是一个值得推广的好案例，他们的调研发现远程之后整体工作效率不降反升。

如果一种模式即没有损失工作效果，又给与了员工一定的自由，那就是应该推行，希望远程办公的趋势能来的更快一些吧。

## 面试解析

整理编辑：[Hello World](https://juejin.cn/user/2999123453164605/posts)

### CRC 实践应用：理论推导

`CRC` 全称叫做循环冗余校验（Cyclic Redundancy Check），是一种根据网络数据包或电脑文件等数据产生**简短固定位数校验码**的一种散列函数。常用于网络数据传输中的差错校验，其特征是信息字段和校验字段的长度可以任意选定。

> 类似作用的还有 **奇偶偶校验、累加和校验等**

#### 理论基础一：多项式运算

`CRC`是基于多项式运算实现的，计算机系统中需要传输的数据可以看做是二进制表示的，例如数值42（ 0x2A） = 0b 10 1010。二进制同时也可以改写成多项式格式表示（简化版）：0b 10 1010 = 2^5 + 2^3 + 2^1（这里将系数为 0 的项删除，并省略了每一项的系数 1）。

CRC 算法推到过程，可以参考《循環冗餘校驗-维基百科》简介部分：

1. 将数据转换成多项式格式，记作 M(x) 作为被除数。
2. 和接收方协定一个多项式记作 K(x)，作为除数 。专有名词被称为**生成多项式**
3. 二者做除法。最终可以表示为 `M(x) * x^n = Q(x) * K(x) - R(x)`。**Q(x)**看做是商，**R(x)**看着为余数。**x^n** 表示左移 n 位（等价于数据低位补 n 个零），n 的取值是生成多项式 K(x) 的位数 - 1。例如上面的 0b 10 1010 位数为 6，所以 n = 5。
4. 然后将 M(x) + R(x) 的和作为新的数据传输到接收端，接收端收到后再除以多项式 K(x)，如果余数为零说明数据没有修改，如果余数不为零说明  M(x) 部分有了变动，即数据发生了改变。

> 这里余数为零并不是绝对的表明数据没修改，存在数据改动后校验余数为零的可能，一般被称为误码率。也是衡量差错校验算法的指标之一。

实际编码中，**K(x)** 并不是随意选取的，而是使用一些已经规定的效率更高的多项式的值，根据 **K(x)** 位数不同，校验函数也命名不同，例如常见的 **CRC-8、CRC-16、CRC-32、CRC-64等**。

例如 CRC-16 对应的生成多项式 PLOY 为：（x^16 + x^15 + x^2 + 1），表示为 0b 1 1000 0000 0000 0101 = 0x18005。

#### 理论基础二：模 2 除法

上面讲了多项式格式转换以及作用。那么模 2 除法是做什么的呢？

在第三步中提到两个多项式做除法，实际这里并不是采取普通的除法运算，而是模 2 除法，特点是在进行余数计算时，针对每一位的加减运算皆采用不进位 & 不借位思想。即:

```cpp
0 - 0 = 0;
0 - 1 = 1;
1 - 0 = 1;
1 - 1 = 0;
```

是不是看着很熟悉，对的，其实模 2 除法中的位加减运算本质上是二进制的位异或运算（`xor`）。

#### CRC 直接计算法

基于以上两个理论，我们总结下 `CRC`算法的步骤：

1. 将要传输的数据用二进制形式展开，并选定一个生成多项式 K，K 的二进制长度为 W+1，则位宽为 W 
2. 在要传输的二进制数据后补 W 个 0，并初始化寄存器值为零。 
4. 用二进制展开后的数据以模 2 除法除以k，数据首位是 1 则商 1，为 0 则商零，最终计算得到的余数即为循环冗余校验和

根据上面的算法思想，我们这里通过一个计算实例验证：

![](https://cdn.zhangferry.com/Images/weekly_53_interview_01.png)

上面的计算过程省略了商为 0 的余数计算，转换为程序逻辑代码如下：

```cpp
#define CRC_WIDTH   3
#define CRC_POLY    0x9     // 1001b

// 这里是需要进行校验的数据
int data = 0xB1C;           // 0b 1011 0001 1100 
// 左移补 0 这里 W = 3
data <<= CRC_WIDTH;
// 初始化结果
int crc = 0;
//  依次针对每一 bit 位进行计算 总位数 = 数据位宽 + 多项式位宽
for(int shift_bit = DATA_WIDTH + CRC_WIDTH;  shift_bit >  0;  shift_bit--) {
    // regs << 1 目的是移除当前寄存器中最高位的值，使用剩余的bit 数据做计算，因为无论最高位是零或者 1，最终计算后都是 0，（最高位为 0 则商零 和 0 做异或， 为 1 则商 1 和多项式做异或，多项式最高位为 1 ）
    // (data >> (shift_bit - 1)) & 0x1是为了获取数据中指定位的值，  & 0x1是为了只取最低位的 1 bit ，示例 1011 就是依次取 1、0、1、1
    // 整句的目的是移除寄存器最高位数据，然后用待计算的数据补充
    crc = (crc << 1)  |  ((data >> (shift_bit - 1)) & 0x1);
    // 获取待计算数据的最高 bit 位是 0 还是 1， 如果商为 1 则需要 xor 异或计算，
    if(crc >> CRC_WIDTH & 0x1)    {
        crc = crc ^ CRC_POLY;
    }
}
```

**CRC 直接计算法看上去就像在循环消除被除数的首位数据**，每一轮计算都可以消除最高位 bit 值，得到余数后，继续计算消除首位。直到无法求商时，最终的余数值就是 CRC。

该思路虽然简单，但是如果需要被检验的数据很大时，每一个 bit 位都需要进行移位 & 异或操作，占用系统运算资源且相对速度慢。所以为了优化效率，采用空间换时间的思路。也就是**驱动表法**

#### 进阶一 驱动表法（查表法）

驱动表思路是一次性计算好多位数据的 CRC 值存储在表中，使用时直接用对应位数的数据和表中的 CRC做异或。

由于计算机系统中一般以字节为最小数据单位，所以 CRC 表一般以 8 bit 作为最小生成单位，**该思路利用了异或结合律(A Xor B Xor C = A Xor ( B Xor C ))。**

示例表位宽简化为 4 bit 如下所示：

![](https://cdn.zhangferry.com/Images/weekly_53_interview_03.png)

观察选中内容两种计算顺序，针对0b 1011每一位数据计算余数的结果和先进行除数的多次异或，然后再和数据 0b 1011异或的结果是一致的，驱动表就是这样实现的，提前计算初始化1011的余数值并存入表中，可以实现该值的重复利用，以空间换取后续同样数据的计算时间。

表的生成代码如下(以一字节为例)，8 位二进制可以表示的数据范围为 0 ~ 255，针对每一个数据生成对应的 CRC 表：

```cpp
#define CRC_POLY    0x11C
static uint64_t crc_table[256];

for (int i = 0; i < (1<<8); i++) {
    uint64_t crc = i;
      /* 每一字节数据都需要计算 8 次以求取 CRC 的异或值 */
    for (int j=8; j > 0; --j) {
        if (crc & 0x80)  /* 判断最高位是否为1 决定商 1 或者商 0 */ {
            /* 最高位为1，不需要异或，往左移一位，然后与 CRC_POLY异或 */
            crc = (crc << 1) ^ CRC_POLY;
        }
        else {
            /* 最高位为0时，不需要异或，整体数据往左移一位 */
            crc = (crc << 1);
        }
    }
    crc_table[i] = crc;
}
```

使用 CRC 表计算数据，当前数据最高位字节作为查表的索引，reg 专业名词叫做寄存器，可以理解为一个双字节的滑动窗口，初始值为 0，沿着数据字节链每次移动一个字节。

计算步骤如下：

1. register左移一个字节，从原始数据中读入一个新的字节到寄存器的低字节位。
2. 利用刚从 register 移出的高位字节作为 index 定位 table 中的一个 CRC 值，因为生成时也是以该数据计算的 CRC 值。
3. 把这个 CRC 值 XOR 到 register 中组成下一次计算的新的数据，此时最高位的字节已经被消除。
4. 如果还有未处理的数据则回到第一步继续执行。

代码示例：

```cpp
unsigned char   buff[] = {0x31, 0x32, 0x33, 0x34};
unsigned int    len  = sizeof(buff);
unsigned char   *pointer;
unsigned int    regs;

pointer = buff;
regs = 0;

// 在数据长度内迭代计算 CRC
while(len--){
    //  (regs>>8)&0xFF 是为了获取寄存器中的高位字节数据
    // (regs<<8)|*pointer++ 操作时将寄存器高位字节移除，然后寄存器低位字节数据用我们需要计算的数据补充，相当于一次滑动操作。
    regs = ((regs << 8) | *pointer++) ^ table[(regs >> 8) & 0xFF];
}

// 处理数据需要扩展的多项式位宽的补位数据，扩展的数据都是零, 根据 CRC_POLY    0x11C 扩展了 8 bit位一个字节
for(int i = 0;  i< 1; i++) {
    regs = (regs << 8) ^ table[(regs >> 8) & 0xFF];
}
```

驱动表法和直接计算法在思路上是很类似的，都是在循环的消除高 bit 位或高位字节的数据。

以上是 CRC 的一个理论推导过程，下期周报将以 `OOMDetecor` 中的实例讲解实际应用中的变体操作有哪些。

- [【脑冻结】CRC我就拿下了](https://www.cnblogs.com/poiu-elab/archive/2012/10/22/2734715.html "【脑冻结】CRC我就拿下了")
- [不要跑，CRC没这么难！（简单易懂的CRC原理阐述）](https://segmentfault.com/a/1190000018094567 "不要跑，CRC没这么难！（简单易懂的CRC原理阐述）")

## 优秀博客

> 本期优秀博客的主题为：App Extension。

1、[iOS - App Extension 整体总结](https://www.cnblogs.com/junhuawang/p/8178276.html "iOS - App Extension 整体总结") -- 来自博客园：俊华

[@我是熊大](https://github.com/Tliens)：本文比较全面的介绍了 App Extension 的种类以及使用方法，平时不怎么使用的 Extension 竟然有十几种。通过此文应该能对 Extension 有个整体的了解。

2、[App与Extensions间通信共享数据](http://yulingtianxia.com/blog/2015/04/06/Communication-between-your-App-and-Extensions/ "App与Extensions间通信共享数据") -- 来自博客：杨萧玉

[@我是熊大](https://github.com/Tliens)：本文利用 WatchKit Extension 实现了 App 与 Watch 之间的通信，介绍了Containing app 与 App Extension 之间如何进行通信和数据共享。

3、[Photo Editing Extension 详解](https://colin1994.github.io/2016/03/12/Photo-Editing-Extension/ "Photo Editing Extension 详解") -- 来自博客：colin

[@我是熊大](https://github.com/Tliens)：本文通过一个 Demo 演示，介绍了 Photo Editing Extension 如何开发。

4、[iOS14 Widget小组件开发(Widget Extension)](https://www.jianshu.com/p/94a98c203763 "iOS14 Widget小组件开发(Widget Extension)") -- 来自简书：Singularity_Lee

[@我是熊大](https://github.com/Tliens)：iOS14 之后出现了非常重要的 Extension，这就是 Widget，桌面小组件，本文十分详细的介绍了如何开发Widget，如果你也有开发需求，推荐阅读。

5、[揭秘 iOS App Extension 开发 —— Today 篇](https://www.jianshu.com/p/bbc6a95d9c54 "揭秘 iOS App Extension 开发 —— Today 篇") -- 来自简书：Cyandev

[@我是熊大](https://github.com/Tliens)：在iOS14之前，是没有桌面组件的，那时候叫做 Today Extension，但在 iOS14 之后，这个 Extension 已经被 Widget 代替。本文借助一个 todo 的 Demo，介绍了 Today Extension。

## 见闻

> 这一周阅读 / 浏览到的有趣的资讯。

1、[Google I/O 2022: 促进知识和计算机技术发展](https://mp.weixin.qq.com/s/bflDfIO1F6xF-tfMEcBjAw) -- 来自公众号：谷歌开发者

[@远恒之义](https://github.com/eternaljust/)：备受期待的开发者科技盛宴 [Google I/O 2022](https://io.google/2022/program/intl/zh/ "Google I/O 2022 官网") 于北京时间 5 月 12 日凌晨 01:00 正式开幕，Sundar Pichai 在大会开幕式上发表了主题演讲。硬件方面，主打性价比 Pixel 6a 售价 449 美元，Pixel 7 / 7 Pro 拥有三款新配色。Pixel Buds Pro 对标 AirPods Pro，是 Google 第一款支持主动降噪的 TWS 耳机。新的硬件 Pixel Watch 会与 Pixel 7 系列一道在 Google 秋季硬件发布会登场。搭载 Google Tensor 芯片的 Pixel 平板将画饼到 2023 年。软件方面，Android 开发领域迎来了大量新的特性与更新，Android 13、Flutter 3、Jetpack、Compose 1.2 beta、Large Screens、Machine Learning、Android Studio Dolphin (2021.3.1) 等等。

2、[告别华尔街十年，我在非洲做起 WiFi 生意](https://mp.weixin.qq.com/s/fote_47nG8AQW-lrkQy9Hw) -- 来自公众号：志象网

[@远恒之义](https://github.com/eternaljust/)：拥有传奇经历的周涛定居在非洲这片“最后十亿人”（last billion）的土地，目前正在肯尼亚创业，做“社区 WiFi”。他在国内做云计算的过程中结实了肯尼亚外交官 David，现在的合伙创始人。David 对肯尼亚目前绝大多数的用户不能廉价上网这件事情耿耿于怀，想要在肯尼亚发展互联网，必须先搞定昂贵的上网资费。周涛提供的公共 WiFi 网费非常低，做到了本土市场价格的 1/10。因地制宜，周涛大量雇佣当地社区青年来服务社区用户，打破了大家对非洲青年的固有印象。通过本地化的管理，公司的规模达到了 200 多人，其中大概只有五六个中国人。

3、[Livid：十年V2EX，复古游戏，和未来](https://www.xiaoyuzhoufm.com/episode/626ba660bf39836fd02b78e9 "Livid：十年V2EX，复古游戏，和未来") -- 小宇宙：枫言枫语

[@zhangferry](zhangferry.com)：本期播客访谈的是 [V2EX](https://www.v2ex.com/) 的作者 Livid。聊了 V2EX 这十年的发展状况、老头环等复古游戏、还有如何用技术解决言论自由等问题。这里还学到一个新词「doomscrolling」，译为末日刷屏，指人们沉溺于刷负面新闻的状态。「互联网是很大的，你如果看到的互联网是这个样子，那是因为你去选择了关注这样的互联网」，关注正向的内容或者干脆不要受屏幕内容的影响，大家也可以好好考虑下。

说会 V 站本身，想想这么一个「简陋」的已经存在十年的 BBS 模样的网站，还依然有着很多忠实用户，其中程序员人群「浓度」一直很高，不免感觉神奇。不求变的东西为什么还能一直存在呢？后来我想了想，一个社区产品真正重要的东西不是花哨的装饰而是氛围，V 站有着其独特，小众的氛围，围绕这个氛围去吸引有相似属性的人。因为没有扩张阶段，这些领域的人会一直维持着较高同兴趣浓度，所以只要基本的功能，表达想法和交流想法的能力具备了，它就是一个完成态了。剩下的交给使用者去创造就行了，嗯，有意思。

4、[Relingo](https://relingo.net/zh/index "Relingo") -- 浏览器插件

[@zhangferry](zhangferry.com)：一个帮助你学习英语的插件，使用起来是这样的。对于一篇英文文章，它可以自动根据设置等级翻译特定词汇，阅读本来就是非常高效的记忆单词技巧，加上这个自动翻译高级词汇的功能，真的非常便捷。还可以将自己不熟悉的单词记录到生词本中，有需要的快用起来吧。

![](https://cdn.zhangferry.com/Images/20220511233016.png)

5、[图解iOS签名背后的原理](https://mp.weixin.qq.com/s?__biz=Mzg3MjcxNzUxOQ==&mid=2247484901&idx=1&sn=83fcf34b5b4b7a63c17742efa3ee20a8&chksm=ceea4845f99dc1537003bdebd2a93d09c9b413ad0cc8c5108cd93d5b84e63e65a30ce0f6c019&token=996185752&lang=zh_CN#rd) -- 公众号：Bo2SS

[@doubleLLL3](https://github.com/doubleLLL3)：文章图文并茂地讲述了iOS签名背后的原理，讲述思路层层递进，容易理解，同时文章也参考了很多优秀的博客，并均在文末指出。

文章保证：阅读完全文会让你拥有轻松解决以下问题的能力：在真机上跑自己的iOS项目、iOS开源库以及公司的iOS项目。另外，文章的终极目标是让你在遇到任何 iOS 签名相关问题时，都能够让问题快速解决。

## 学习资料

整理编辑：[zhangferry](https://zhangferry.com)

### Skr_Learning

**地址**：https://github.com/Kiprey/Skr_Learning

这里将定期记录着一些与`Sakura`师傅以及一群小伙伴共同学习的内容与进度。是一篇学习汇报，内容包括但不限于C++ STL、编译原理、LLVM IR Pass代码优化、CSAPP Lab、uCore操作系统等等。持续更新ing...

感觉 Github 也是一种很好的记录学习过程的平台！

## 工具推荐

整理编辑：[CoderStar](https://mp.weixin.qq.com/mp/homepage?__biz=MzU4NjQ5NDYxNg==&hid=1&sn=659c56a4ceebb37b1824979522adbb15&scene=18)

### IINA

**地址**：https://iina.io/

**软件状态**：免费

**软件介绍**：

适用于 `macOS` 的 **现代** 媒体播放器，`IINA` 由开源媒体播放器 `mpv` 提供支持，几乎可以播放您拥有的所有媒体文件。

![iina](http://cdn.zhangferry.com/sc-sky.png)

## 关于我们

iOS 摸鱼周报，主要分享开发过程中遇到的经验教训、优质的博客、高质量的学习资料、实用的开发工具等。周报仓库在这里：https://github.com/zhangferry/iOSWeeklyLearning ，如果你有好的的内容推荐可以通过 issue 的方式进行提交。另外也可以申请成为我们的常驻编辑，一起维护这份周报。另可关注公众号：iOS 成长之路，后台点击进群交流，联系我们，获取更多内容。

### 往期推荐

[iOS 摸鱼周报 #52 | 如何规划个人发展](https://mp.weixin.qq.com/s/45ftt4AC2C5Ts8Zt3sWvJA)

[iOS 摸鱼周报 #51 | 游戏版号恢复发放](https://mp.weixin.qq.com/s/ogjhELipiVFRaYJkT2NQwA)

[iOS 摸鱼周报 第五十期](https://mp.weixin.qq.com/s/6IS0RlytWxjeRHyh0f2fXA)

[iOS 摸鱼周报 第四十九期](https://mp.weixin.qq.com/s/6GvVh8_CJmsm1dp-CfIRvw)

![](https://cdn.zhangferry.com/Images/WechatIMG384.jpeg)
