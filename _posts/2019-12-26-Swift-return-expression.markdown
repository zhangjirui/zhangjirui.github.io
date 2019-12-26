---
layout: post
title:  "Swift return expression"
---

![]({{site.url}}/assets/2019122600.jpeg)

   
   
   
直接上例子：
![]({{site.url}}/assets/2019122601.png)

`Expression following 'return' is treated as an argument of the 'return'`   
为何上述代码会出现warning？

我们直接看Swift官方语法说明：
>A return statement can consist of only the return keyword, or it can consist of the return keyword followed by an expression, as shown below.
>
>- return
>- return expression
>
>When a return statement is followed by an expression, the value of the expression is returned to the calling function or method.

看到这里，就清楚warning的来源了，`a = a + 1` 被当做了return 的 expression。

进一步研究，编译器如何解析return呢？在 ParseStmt.cpp 中 `Parser::parseStmtReturn` 函数中，我们找到了如下注释：
```
  // Handle the ambiguity between consuming the expression and allowing the
  // enclosing stmt-brace to get it by eagerly eating it unless the return is
  // followed by a '}', ';', statement or decl start keyword sequence.
```
按照该注释，要想消除该warning，很简单，在 return 后面添加`}`或者`;`即可。当然，此处只是举个例子，会出现新的warning。
![]({{site.url}}/assets/2019122602.png)
