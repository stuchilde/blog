# 字符编码ASCII、Unicode、UTF-8

今天在看一些面试题目的时候突然看到关于字符编码的问题，想到自己也会经常遇到这个问题，每次都是去网上直接搜索答案，并不明白其中的原理，这次就彻底的花了一下午的时间去了解关于字符编码的问题，看完之后觉得收获颇多，这篇文章就来简单讲解下我的学习过程，希望能帮助到大家。

<!-- more -->

### 一、ASCII

**ASCII**(American Standard Code Information Interchange，**美国信息交换标准代码**)，是基于拉丁字母的一套计算机编码系统，它主要用于显示现代英语。（解释来源于[wiki](https://zh.wikipedia.org/wiki/ASCII)）

ASCII是一种单字节编码的编码系统，**迄今为止它定义了128个字符**，我们知道单字节总共有八位（ox00-oxFF），但128个字符只会用到七位，我们就规定最前面一位为0，下图中截取了部分ASCII编码的映射关系

![image-20181005113341383](https://blog-private.oss-cn-shanghai.aliyuncs.com/20200315184445.jpeg)

<div style="font-size:13px;color:gray;text-align:center">部分可显字符</div>

**缺点：**ASCII编码只能表示128个字符，那对于一些其他国家的语言，比如法语字母上方是有标注的，于是一些欧洲国家就决定使用空闲下来的最高位编码纳入新的符号，比图法语中à的编码为129，这样一来这些欧洲国家使用的ASCII就可以表示256个字符，这样就衍生出了**EASCII**（**Extended ASCII**，延伸美国标准信息交换码），EASCII码比ASCII码扩充出来的符号包括表格符号、计算符号、希腊字母和特殊的拉丁符号。

但是这样又出现了新的问题，不同的国家用的语言可能不同。因此256个字符来编码肯定是不够的，至于亚洲国家来说更是如此，光汉字就有10万个左右，一个字节最多只能表示256个字符，**那就必须要使用多字节编码来表示，**这样就急需统一不同国家的一些字符，以便于整个计算机的发展和推广。Unicode编码方式的出现就解决了这个问题。

### 二、Unicode

**Unicode**是计算机科学领域里的一项行业标准，它对世界上大多数的文字系统进行了整理、编码，使得计算机可以更简单的处理和显示这些文字。

Unicode编码旨在收集全球所有的字符。为每个字符分配一个唯一的字符编码即代码点（code-point），用U+后面跟着的十六进制数表示。**所有字符按照使用的频率被分为17个平面（编号为0-16）**，即基本多语言平面和增补平面。基本多语言平面又称平面0，收集了最为广泛的字符，代码点从**U+0000到U+FFFF**，每个平台有2<sup>16</sup>=65536个码点；增补平面从1～16，每个增补平面也是2<sup>16</sup>=65536个码点，总共有**17*65536=1114112个码点**，下图就是Unicode码点在平面的分布。

![img](https://blog-private.oss-cn-shanghai.aliyuncs.com/20200315184457.jpeg)

<div style="font-size:13px;color:gray;text-align:center">Unicode平面分布和码点空间</div>

例如中文`张`用Unicode编码为`U+5F20`，换成二进制`0101111100100000`，也就是说中文`张`至少需要两个字节。如果表示其他更大的字符可能需要3个字节、四个字节甚至更多。

这时一个很严重的问题就出现了，如何区分ASCII和unicode，计算机怎么区分编码的分界点，怎么知道三个字节长度的编码的到底是三个单字节还是一个单字节加上一个双字节，如果将长度固定为三字节或者四字节那对于英文字母来说前几个字节都是0，会造成空间的极大浪费（3-4倍），这是无法接受的，Unicode很胀一段时间无法推广，直到互联网的出现。

### 三、UTF-8

**UTF-8（8-bit Unicode Transformation Format）是一种针对Unicode的长度可变的字符编码，也是一种前缀码**。一般用1-4个字节来表示Unicode字符，也是目前应用最广泛的一种Unicode编码方式。UTF-8编码算法有以下几个特点：

1. **首字节码用来表示编码的字节数**：如果首字母是0开头，则表示单字节编码；如果以110开头，则表示两个字节编码；如果以1110开头，表示三个字节编码，以此类推。

2. 除了首字节编码外，**用10开头表示多字节编码的后续字节**，如下图表示了从unicode编码转化为UTF-8编码的规则。

   ![img](https://blog-private.oss-cn-shanghai.aliyuncs.com/20200315184502.jpeg)

<div style="font-size:13px;color:gray;text-align:center">UTF-8编码规则</div>

3. **与ASCII编码方式完全兼容**：从U+0000到U+007F范围（十进制0～127）码点对应的就是ASCII字符集中的字符，采用单字节编码，编码方式与ASCII完全相同。

下面我将简单举个例子来说明一个unicode编码怎么转化为UTF-8编码：

中文`引`的Unicode为`U+5F15`（`0101 1111 0001 0101`）有效比特位是16，根据上面的编码规则满足第三行的规则，因此按照`1110xxxx 10xxxxxx 10xxxxxx`的规则最后得到`引`的UTF-8编码为1110<span style="color:red">0101</span> 10<span style="color:red">111100</span> 10<span style="color:red">010101</span>，转化为十六进制为D5BC95，红色的部分就是`引`的Unicode编码。

### 总结

以上都是一些关于常用编码方式的发展过程以及他们的一些原理解析，可能并不够完全解决乱码的问题，其实编码方式是一件极其复杂的问题。每个国家都有自己特定的文字系统，在计算机刚刚发展的那些年，很多国家都提出了解决自己国家文字的编码方式，且并不能与其他国家的相兼容，所以经常会遇到一些乱码的问题，我们经常在邮件中看到的乱码就是由于发送者和接受者的编码方式不同导致的。随着近些年互联网的兴起，大家开始使用统一的UTF8编码，乱码问题可能会出现的更少了。



参考内容：

[ASCII WIKI](https://zh.wikipedia.org/wiki/ASCII)

[Unicode WIKI](https://zh.wikipedia.org/wiki/Unicode)

[UTF-8 WIKI](https://zh.wikipedia.org/wiki/UTF-8)

[Unicode 及编码方式概述](https://www.ibm.com/developerworks/cn/java/unicode-programming-language/index.html)