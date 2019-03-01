---
layout: post
title:  "Initializer element is not a compile time constant"
---

```
//  ViewController.m

#import "ViewController.h"

NSObject *obj = [[NSObject alloc] init];

@implementation ViewController

@end
```

```
//  ViewController.m

#import "ViewController.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    static NSObject *obj = [[NSObject alloc] init];
}

@end
```

上面两块代码，编译时均会报错`Initializer element is not a compile-time constant`。

报错原因是：In C and Objective-C, static variables inside functions or global variables must be initialized with compile-time constants, whereas C++ and Objective-C++ are more lenient and allow non-compile-time constants.  

解决方法：
- 将 ViewController.m 的后缀改为 ViewController.mm，则不会报错
- 或者，调整初始化方法的位置，如下：

```
//  ViewController.m

#import "ViewController.h"

NSObject *obj;

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    obj = [[NSObject alloc] init];
}

@end
```

```
//  ViewController.m

#import "ViewController.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    static NSObject *obj;
    obj = [[NSObject alloc] init];
}

@end
```

参考链接：  
[statckoverflow](https://stackoverflow.com/questions/6143107/compiler-error-initializer-element-is-not-a-compile-time-constant)  
[statckoverflow](https://stackoverflow.com/questions/12304740/initializer-element-is-not-a-compile-time-constant-why)
