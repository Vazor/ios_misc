# Markdown语法

<!-- 注释：使用Mou的教程 -->

## 粗体（Strong）& 斜体（Emphasize） 

**粗体** or __粗体__ ( Cmd + B )

*斜体* or _斜体_ ( Cmd + I )

## 引用（Blockquotes）

> 右箭头表示引用。使用非转义的引用： &gt; 
>> 引用中的引用

## 超链接（Links & Email）

自动连接：<admin@jeffw.us> <http://jeffw.us>

[不带标题的连接](http://jeffw.us)

[带标题的连接](http://jeffw.us "Jeff的主页").

[利用引用的连接][id]

[id]: http://jeffw.us "Jeff的主页"

## 图片（Images）

行内图片 ![普通图片](https://www.baidu.com/favicon.ico "百度的favicon"), title为可选.

![利用引用的图片][2]

[2]: https://www.baidu.com/favicon.ico "百度的favicon"

## 行内代码（Inline code）& 代码块（Block code）& 代码栏（Fenced Code Blocks）

行内代码使用`英文的重音符（backtick)`.

	// 每行行首使用1个Tab或4个空格
    printf("hello Jeff!"); 

```
// 代码栏：起始和结尾使用等数量的3个或以上的英文重音符（`）
printf("hello Jeff!"); 
```

##  有序列表（Ordered Lists）

行首：数字 + . + 空格

1. Ordered list item
2. Ordered list item
3. Ordered list item

## 无序列表（Unordered Lists）

行首：* + 空格 OR - + 空格 OR + + 空格（Ctrl + 回车）

* Unordered list item
* Unordered list item
- Unordered list item
- Unordered list item
+ Unordered list item

## 嵌套列表

- 嵌套列表
	+ 2个或4个空格，或1个tab
    	* +-*都一样，而且可以循环使用
      		1. 可以使用有序列表
      		
- 嵌套列表
---

1. Ordered list item
	1. Ordered list item
3. Ordered list item


## 硬换行（Hard Linebreak）

行尾使用2个或4个空格会产生一个硬换行，HTML里称为 `<br />` 。( Ctrl + 回车 )  
上一行行尾使用了2个空格。

## 分割线（Horizontal Rules）

行首：三个以上的*号或-号:

***

---

- - - -

## 标题（Headers）

Setext-style:

This is H1
==========

This is H2
----------

atx-style:

# This is H1
## This is H2
### This is H3
#### This is H4
##### This is H5
###### This is H6


## 脚注（Footnotes）

Footnotes work mostly like reference-style links. A footnote is made of two things: a marker in the text that will become a superscript number; a footnote definition that will be placed in a list of footnotes at the end of the document. A footnote looks like this:

点击脚注查看.[^1]

[^1]: 数字是可选的。而且文档最后只会显示实际有用的注释。

## 删除线（Strikethrough）

~~左右各使用两个波浪线符号~~

## 表格（Tables）

一个简单地表格如下：

First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell

也可以左右添加竖线：

| First Header | Second Header | Third Header |
| ------------ | ------------- | ------------ |
| Content Cell | Content Cell  | Content Cell |
| Content Cell | Content Cell  | Content Cell |

指定对其方式：

First Header | Second Header | Third Header
:----------- | :-----------: | -----------:
靠左          | 居中          | 靠右
靠左          | 居中          | 靠右

## 转义字符

Markdown中的转义字符为\：

\\ 反斜杠  
\` 反引号  
\* 星号  
\_ 下划线  
\{\} 大括号  
\[\] 中括号  
\(\) 小括号  
\# 井号  
\+ 加号  
\- 减号  
\. 英文句号  
\! 感叹号  
\> 大于号

## 其它

文本中可直接用html标签，但是要前后加上空行。
