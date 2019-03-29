---
title: 关于runtime
date: 2019-03-28 17:59:44
tags:
---

#    关于runtime

1.什么是runtime？
1> runtime是一套底层的C语言API（包含很多强大实用的C语言数据类型、C语言函数）
2> 实际上，平时我们编写的OC代码，底层都是基于runtime实现的

也就是说，平时我们编写的OC代码，最终都是转成了底层的runtime代码（C语言代码）
2.runtime有啥用？
1> 能动态产生一个类、一个成员变量、一个方法
2> 能动态修改一个类、一个成员变量、一个方法
3> 能动态删除一个类、一个成员变量、一个方法

<!-- more -->

3.常见的函数、头文件

#import : 成员变量、类、方法
Ivar class_copyIvarList : 获得某个类内部的所有成员变量
Method class_copyMethodList : 获得某个类内部的所有方法
Method class_getInstanceMethod : 获得某个实例方法（对象方法，减号-开头）
Method class_getClassMethod : 获得某个类方法（加号+开头）
method_exchangeImplementations : 交换2个方法的具体实现

4.常用方法

//获取属性列表

objc_property_t propertyList = class_copyPropertyList([self class], &count);
for (unsigned int i=0; i<count; i++) {

const char propertyName = property_getName(propertyList[i]);
NSLog(@”property—->%@”, [NSString stringWithUTF8String:propertyName]);
}

//获取方法列表
Method *methodList = class_copyMethodList([self class], &count);
for (unsigned int i; i<count; i++) {
Method method = methodList[i];
NSLog(@”method—->%@”, NSStringFromSelector(method_getName(method)));
}

//获取成员变量列表
Ivar ivarList = class_copyIvarList([self class], &count);
for (unsigned int i; i<count; i++) {
Ivar myIvar = ivarList[i];
const char ivarName = ivar_getName(myIvar);
NSLog(@”Ivar—->%@”, [NSString stringWithUTF8String:ivarName]);
}

//获取协议列表
__unsafe_unretained Protocol *protocolList = class_copyProtocolList([self class], &count);
for (unsigned int i; i<count; i++) {
Protocol myProtocal = protocolList[i];
const char *protocolName = protocol_getName(myProtocal);
NSLog(@”protocol—->%@”, [NSString stringWithUTF8String:protocolName]);
}

5.拦截调用

在方法调用中说到了，如果没有找到方法就会转向拦截调用。
那么什么是拦截调用呢。
拦截调用就是，在找不到调用的方法程序崩溃之前，你有机会通过重写NSObject的四个方法来处理。


(BOOL)resolveClassMethod:(SEL)sel;
(BOOL)resolveInstanceMethod:(SEL)sel;
//后两个方法需要转发到其他的类处理

(id)forwardingTargetForSelector:(SEL)aSelector;
(void)forwardInvocation:(NSInvocation *)anInvocation;
5.1 第一个方法是当你调用一个不存在的类方法的时候，会调用这个方法，默认返回NO，你可以加上自己的处理然后返回YES。
5.2 第二个方法和第一个方法相似，只不过处理的是实例方法。
5.3 第三个方法是将你调用的不存在的方法重定向到一个其他声明了这个方法的类，只需要你返回一个有这个方法的target。
5.4 第四个方法是将你调用的不存在的方法打包成NSInvocation传给你。做完你自己的处理后，调用invokeWithTarget:方法让某个target触发这个方法。
