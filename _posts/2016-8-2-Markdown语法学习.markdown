---
layout: post
title:  "Markdown语法学习"
date:   2016-08-02 下午7:21
categories: jekyll update
---

下面是最常用的markdown语法，每个语法举例，上面是代码，下面是效果。
## 1. 标题大小

```
井号和文字之间最好有个空格，不然在某些版本上会显示不出效果，最多六级

# 测试一下标题大小
## 测试一下标题大小
### 测试一下标题大小
#### 测试一下标题大小
##### 测试一下标题大小
###### 测试一下标题大小
####### 测试一下标题大小
```

# 测试一下标题大小
## 测试一下标题大小
### 测试一下标题大小
#### 测试一下标题大小
##### 测试一下标题大小
###### 测试一下标题大小
####### 测试一下标题大小

## 2. 反斜杠
```
\# 测试一下标题大小
\## 测试一下标题大小
\### 测试一下标题大小
\#### 测试一下标题大小
\##### 测试一下标题大小
\###### 测试一下标题大小
```

\# 测试一下标题大小  
\## 测试一下标题大小  
\### 测试一下标题大小  
\#### 测试一下标题大小  
\##### 测试一下标题大小  
\###### 测试一下标题大小  

## 3. 无序列表

```
* 文本
* 文本
* 文本
```
- 文本
- 文本
- 文本

## 4. 有序列表
```
1.  文本
2.  文本
3.  文本
```
1.  文本
2.  文本
3.  文本

## 5. 链接和图片

```
[百度](http://www.baidu.com)
<http://www.baidu.com>
我经常去的几个网站[百度][1]、[搜狐][2]、[新浪][]  
    [1]:http://www.baidu.com  
    [2]:http://www.sohu.com  
    [新浪]:http://www.sina.com

```
[百度](http://www.baidu.com)

<http://www.baidu.com>

我经常去的几个网站[百度][1]、[搜狐][2]、[新浪][]
[1]:http://www.baidu.com
[2]:http://www.sohu.com
[新浪]:http://www.sina.com

## 6. 插入图片
```
![](http://guohongwei719.github.io/images/20150725/1.png)
```
![](http://guohongwei719.github.io/images/20150725/1.png)


## 7. 引用
```
> 一盏灯，一篇昏黄；一简书，一杯淡茶。守着那一份淡定，品读属于自己的寂寞。保持淡定，餐能欣赏到最美丽的风景！保持淡定，人生从此不再寂寞。
```
> 一盏灯，一篇昏黄；一简书，一杯淡茶。守着那一份淡定，品读属于自己的寂寞。保持淡定，餐能欣赏到最美丽的风景！保持淡定，人生从此不再寂寞。

## 8. 粗体和斜体，粗斜体
```
*一盏灯*，一篇昏黄；**一简书**，***一杯淡茶***。守着那一份淡定，品读属于自己的寂寞。
```
*一盏灯*，一篇昏黄；**一简书**，***一杯淡茶***。守着那一份淡定，品读属于自己的寂寞。

## 9. 删除线
```
~~这里的文字会被无情的划上一根横线~~
```
~~这里的文字会被无情的划上一根横线~~

## 10. 代码引用
```
`NSLog(@"test")`
```
`NSLog(@"test")`

## 11. 多行代码引用
```

    ```
    UILabel *label = [[UILabel alloc] initWithFrame:CGRectZero];
    label.font = [UIFont systemFontOfSize:12];
    label.textColor = [UIColor lightGrayColor];
    ```
```
UILabel *label = [[UILabel alloc] initWithFrame:CGRectZero];  
label.font = [UIFont systemFontOfSize:12];  
label.textColor = [UIColor lightGrayColor];


## 12. 水平分隔线
```
---
```
---

## 13. 表格
```
dog | bird | cat
------|-------|----
foo  | foo  | foo
bar  | bar  | bar
baz | baz  | baz


| Tables |  Are | Cool |
| ----------|:------:|------:|
| col 3 is | right-aligned|$1600|
|col2 is | centered | $12 |
| zebra stripes | are neat | $1|
```
dog | bird | cat
------|-------|----
foo  | foo  | foo
bar  | bar  | bar
baz | baz  | baz


| Tables |  Are | Cool |
| ----------|:------:|------:|
| col 3 is | right-aligned|$1600|
|col2 is | centered | $12 |
| zebra stripes | are neat | $1|




























































