---
layout: post
title:  "for(... in ...) or enumerateObjectsUsingBlock"
---

> Xcode 11.2.1   
> iPhone 8 13.2.2 模拟器

### 只遍历所有对象

```
NSMutableArray *myArr = [[NSMutableArray alloc] init];
for (int i = 0; i < 10000; i ++) {
    [myArr addObject:[NSNumber numberWithInt:i]];
}

NSDate *date0 = [[NSDate alloc] init];
[myArr enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    
}];
NSLog(@"enum %lf", [[[NSDate alloc] init] timeIntervalSinceDate:date0]);

date0 = [[NSDate alloc] init];
for (NSNumber *num in myArr) {
    
}
NSLog(@"for in %lf", [[[NSDate alloc] init] timeIntervalSinceDate:date0]);

```

- `for/in` 0.000044
- `enumerateObjectsUsingBlock` 0.000495

### 遍历对象并执行耗时操作

```
NSDate *date0 = [[NSDate alloc] init];
[myArr enumerateObjectsWithOptions:NSEnumerationConcurrent usingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    [self consumeSomeTime];
}];
NSLog(@"enum %lf", [[[NSDate alloc] init] timeIntervalSinceDate:date0]);

date0 = [[NSDate alloc] init];
for (NSNumber *num in myArr) {
    [self consumeSomeTime];
}
NSLog(@"for in %lf", [[[NSDate alloc] init] timeIntervalSinceDate:date0]);
```

- `for/in` 0.156068
- `enumerateObjectsUsingBlock` 0.026496

### 结论

- 一般情况下，`for/in` 要比 `enumerateObjectsUsingBlock` 快；
- 但是，针对于可并发执行的情形，`enumerateObjectsUsingBlock` 原生支持并发操作，明显耗时更短。

另外，上述并发，也可用`dispatch_apply`实现。

```
dispatch_apply([myArr count], dispatch_get_global_queue(QOS_CLASS_USER_INTERACTIVE, 0), ^(size_t index) {
    [self consumeSomeTime];
});
```

其实，通过断点也可发现，`NSEnumerationConcurrent` 内部也是通过 `dispatch_apply` 实现的。   
![]({{site.url}}/assets/20191216.png)

相关阅读：  
[NSFast​Enumeration / NSEnumerator](https://nshipster.com/enumerators/)    
[When to use enumerateObjectsUsingBlock vs. for](https://stackoverflow.com/questions/4486622/when-to-use-enumerateobjectsusingblock-vs-for) 
