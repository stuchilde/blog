# Markdown新手入门

  如果你还没学过Markdown，那你就out了，如果你还没学过那就跟着Dior同学一起学习下吧，至少入个门呀，这样也跟可以跟别人吹吹牛啥的。随着最近频繁使用Markdown，发现真的很好用,  语法很简单，很实用，我觉得这可能是Markdown最大的优点。我们平时写文章用富文本编辑器会发现不同的富文本编辑器很不统一，每次想要加个什么样式要找半天，这样其实效率非常低。而Markdown正好解决了这个问题，统一了一些最常用样式的语法，这样我们就可以专注于写作上，而不用花很多时间去修改样式。现在大多数的博客网站都支持Markdown语法编辑，我们只要在本地用Markdown语法写好一篇文章就可以直接复制到其他博客网站上，很方便并且不存在兼容性的问题。

说了那么多的关于Markdown的东西，没听过这个概念的同学可能比较懵逼，Markdown是什么鬼？

引用wiki对Markdown的解释：

>  **[Markdown](https://en.wikipedia.org/wiki/Markdown) is a lightweight markup language with plain text formatting syntax**

其实翻译过来很简单，就是一种用于文本格式化的标记语言。

---

## Markdown的优点

* **专注于写作的内容，而不是排版的样式**
* **可读性高，学习成本低**
* **纯文本内容，兼容性好** 
* **可以很方便的导出成HTML文本和PDF文件**
* **可以使用Git进行版本控制**

## 好用的Markdown软件

*本人用的是Mac电脑，所以推荐的Markdown软件都是Mac版本的*

* [**Mou**](http://25.io/mou/)这是推荐使用最多的Markdown软件，但是悲催的是最新的Sierra版本有Bug，据说好像Mou团队已经不维护这个项目了。

  ![图片已损坏](https://user-gold-cdn.xitu.io/2018/1/14/160f3eb29b17dcad?w=2028&h=1452&f=jpeg&s=357436)

* [**Typora**](https://typora.io/)是我自己用的软件，这个软件最大的特点是简单并且功能很强大，而且开源和跨平台。我自己用下来发现最大的优点就是写的时候自动转换格式，不用两列的预览方式。而且看起来很干净。

![typora](https://user-gold-cdn.xitu.io/2018/1/14/160f3eb29ba8ba55?w=523&h=466&f=jpeg&s=17015)

* [**MacDown**](http://macdown.uranusjr.com/)是一款开源的Mac平台下的Markdown编辑器

  ![MacDown](https://user-gold-cdn.xitu.io/2018/1/14/160f3eb299c966bd?w=1292&h=736&f=jpeg&s=159306)

* [**iA Writer**](https://ia.net/writer/about/)

![iA Writer](https://user-gold-cdn.xitu.io/2018/1/14/160f3eb2a44e5b19?w=1122&h=694&f=jpeg&s=247784)



* [**Typed**](https://www.codebots.co.uk/)

![Typed](https://user-gold-cdn.xitu.io/2018/1/14/160f3eb2a28b3c9d?w=2880&h=1800&f=jpeg&s=388363)



* [**Byword**](https://bywordapp.com/)

![Byword](https://user-gold-cdn.xitu.io/2018/1/14/160f3eb29d9fa283?w=800&h=500&f=jpeg&s=56411)

* 还有什么好的Markdown欢迎推荐哟！

## Markdown的语法

* **兼容HTML**

  这个语法特点与 <em>Markdown</em> 语言的初衷有关，Markdown就是希望成为适应于网络的书写语言。

  在写一些常见的区块元素(例如: `<div>`  、`<table>`、`<p>`)的时候要注意必须在前后加上空格将其区分开来，并且要求他它们开始和结束的标签不能用空格或者制表符来缩进。

  *下面是一段html语法的表格*

  ```html
  <table>
  	<tr>
  		<td>Foo</td>
  	</tr>
  </table>
  ```

  请注意在上面HTML区块中的Markdown语法不会被转换的。比如你在HTML区块中使用Markdown样式的\*\*强调\*\*是没有加粗效果的。

  如果不希望某个Markdown语法的关键词自动转化样式，可以在字符前面加`\`转义

### 区块元素



#### 1.标题

在行首插入1-6个#，分别对应HTML标签h1-h6

\#\# 			这是h2

\#\#\#			这是h3

\##\#\#\#\# 	这是h6



#### 2.区块引用

* 在每行前面加上`>`

  ```
  > This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
  > This is a block title
  ```



* 区块引用可以嵌套

  ```
  > > > This is a nested blockquote
  ```

* 区块引用支持其他Markdown语法

  ```
  > ## This is a h2 title
  > **strong**
  ```

#### 3.列表

*Markdown支持有序列表和无序列表*

* 有序列表

  规则是数字+英文句号+空格

  ```
  1. Apple
  2. Pear
  3. Banana
  ```

  如果你写成了

  ```
  1. Apple
  1. Pear
  1. Banana
  ```

   或者甚至是

  ```
  6. Apple
  3. Pear
  2. Banana
  ```


  其实上面的结果都是一样的，都是转化为第一个，建议还是从第一个按顺序写，说不定什么时候Markdown就支持了有序列表的start属性。

  当然有时候会不小心产生有序列表

  `2018. This is a grate year`

  解决方法在前面中提到过，就是在英文逗号前加个转义字符`\`，这样就避免了自动转义的问题。

  `2018\. This is a greate year`

* 无序列表

  *使用星号(\*)、加号(+)、减号(-)作为列表标记*

  ```
  * Red
  * Black
  * Blue
  等同于:
  + Red
  + Block
  + Blue
  也等同于:
  - Red
  - Black
  - Blue
  ```

#### 4.代码块

*这对于程序员来说无疑是福音，支持语法高亮，超级好用*

* 块代码

  \`\`\`表示代码块，其语法example如下：

  ```c++
  #include <iostream>
  #include <cstdio>
  using namespace std;
  int main(){
    int a, b;
    cin>>a>>b;
    cout<<a+b<<endl;
  }
  ```

  

* 行内代码块

  \`表示行内代码块

  ```
  `This is inline codequote example`
  ```

#### 5.分割线

*在一行使用三次以上星号(\*)、减号(-)来建立一个分隔符，行内不能有其他的东西，也可以在它们中间插入空格*

```
***

* * *

---

- - -
```



#### 6.表格

Markdown中表格是个比较头疼的东西，语法比较复杂，但是有些软件可以支持一键插入，下面用个例子简单讲下Markdown中的table语法。

```

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | center        | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |
| default left  |  python       | right |
```

| Tables        |   Are    |  Cool |
| ------------- | :------: | ----: |
| col 3 is      |  center  | $1600 |
| col 2 is      | centered |   $12 |
| zebra stripes | are neat |    $1 |
| default left  |  python  | right |

默认是居左对齐，语法为`| --- |`，居中的语法为`|: --- :|`，居右的语法为`| ---:|`，我的事例代码很整齐，其实在Markdown语法规则中并不需要对齐这么整齐(原谅我的洁癖:smile:)。

## 区段元素

#### 1.连接

*Markdown支持两种形式的链接语法*

* 行内式

  行内式的Markdown语法`[链接文本](链接)`，如果还需要加上链接的标题的话直接在链接后面用引号或者括号包裹标题

  **Examples:**

   `[Dior](http://www.todaycoder.cn)`    

  `This is [an example](http://www.todaycoder.cn) link.`

  `This is [an example](http://www.todaycoder.cn "dior") has title attribute.`

* 参考式

  参考式的Markdown语法`[链接文本][链接标记]`，后面再加上`[链接标记]: 链接`，其实这两部是不分先后顺序的，建议把这些链接统一写在文章的底部，这样也便于统一管理和修改。

  **Examples**

  ```
  [Example][id]
  [id]: http://www.todaycoder.cn "example"
  [id]: http://www.todaycoder.cn 'example'
  [id]: http://www.todaycoder.cn (example)
  ```

  ```
  [Google][]
  [Google]: http://www.google.com
  或者
  [Google][0]
  [0]: http://www.google.com
  ```

  下面三种例子以供大家参考：

  **参考式链接**

  ```
  I get 10 times more traffic from [Google] [1] than from [Yahoo] [2] or [MSN] [3].
  [1]: http://google.com/        "Google"
  [2]: http://search.yahoo.com/  "Yahoo Search"
  [3]: http://search.msn.com/    "MSN Search"
  ```
  **链接名称**

  ```
  I get 10 times more traffic from [Google][] than from [Yahoo][] or [MSN][].
  [google]: http://google.com/        "Google"
  [yahoo]:  http://search.yahoo.com/  "Yahoo Search"
  [msn]:    http://search.msn.com/    "MSN Search"
  ```

  **行内式**

  ```
  I get 10 times more traffic from [Google](http://google.com/ "Google")
  than from [Yahoo](http://search.yahoo.com/ "Yahoo Search") or
  [MSN](http://search.msn.com/ "MSN Search").
  ```

  ***大家可以根据具体需求自己选择，如果链接很多的话建议使用上面两种方法***

#### 2.图片

Markdown用一种和链接很类似的方法来标记图片，同样也允许两种样式：*行内式和参考式*

目前Markdown还不支持设置宽高，如果要设置宽高的话可以使用`img`标签

Markdown插入图片的语法： `![Alt text][id]`，这与插入链接的语法很相似，就不多说了，直接来几个例子大家就明白了。

```
![图片已损坏](/path/img/img.jpg  'title')

![iamge hsa broken](/path/img/img.png  "title")

![image][id]
[id]: /paht/img/img.png (title)

![image][]
[image]: /path/img/img.png
```



讲了这么多你学会了么？要是有什么问题或者不同的见解欢迎交流哟！[邮箱](mailto:todaycoder001@gmail.com)