---
title: iOS网易云音乐锁屏界面实现
date: 2019-03-28 18:23:05
tags:
---

完整的实现思路:

App如果需要在锁屏界面上显示相关的信息和按钮, 必须先开启远程控制事件(Remote Control Event), 否则锁屏界面只显示滑动解锁.
实现锁屏界面信息, 将歌曲的相关信息更新到锁屏界面上
实现锁屏界面的事件处理, 在锁屏界面和上拉的快速功能菜单中实现播放控制

远程控制事件的实现

在iOS7.1之前, 远程控制事件主要涉及以下三个方法:

开始接收远程控制事件
结束接收远程控制事件
触发远程控制事件后的捕获处理
官方文档对这三个方法的描述如下, 这里做了简单的翻译.

开始接收远程控制事件

<!-- more -->

Declaration

- (void)beginReceivingRemoteControlEvents
让App开始接收远程控制事件, 该方法属于UIApplication类

Discussion

在iOS7.1之后, 使用MPRemoteCommandCenter的共享对象来注册远程控制事件. 当使用shared command center时, 不需要再调用该方法.
该方法会开始使用事件响应链来传递远程控制事件. 远程控制事件是当耳机和外部附件意图控制App的多媒体表现时发出的命令. 要停止远程控制事件的接收, 必须调用endReceivingRemoteControlEvents方法

结束接收远程控制事件

Declaration

- (void)endReceivingRemoteControlEvents
让App停止接收远程控制事件, 该方法属于UIApplication类

Discussion

在iOS7.1之前, 使用shared MPRemoteCommandCenter对象来注册远程控制事件. 当使用shared command center时, 不需要再调用该方法.
该方法会停止通过事件响应链来传递远程控制事件. 远程控制事件是当耳机和外部附件意图控制App的多媒体表现时发出的命令.

远程控制事件的捕获处理

Declaration

- (void)remoteControlReceivedWithEvent:(UIEvent *)event
当远程控制事件发生时触发该方法, 该方法属于UIResponder类

Discussion

远程控制事件是由外部附件(包括耳机)所发出的命令. 应用需要响应这些命令来控制音频或视频媒体的对用户的表示. 事件响应者通过检查事件的subtype, 来判断命令的意图. 比如UIEventSubtypeRemoteControlPlay为播放操作, 然后做相关处理

要允许远程控制事件的传递, 需要调用UIApplication的beginReceivingRemoteControlEvents方法; 要关闭远程控制事件的传递则调用endReceivingRemoteControlEvents

项目中的代码实现

(BOOL)application:(UIApplication )application didFinishLaunchingWithOptions:(NSDictionary )launchOptions {
// 在App启动后开启远程控制事件, 接收来自锁屏界面和上拉菜单的控制
[application beginReceivingRemoteControlEvents];

return YES;
}

(void)applicationWillTerminate:(UIApplication *)application
{
// 在App要终止前结束接收远程控制事件, 也可以在需要终止时调用该方法终止
[application endReceivingRemoteControlEvents];
}

// 在具体的控制器或其它类中捕获处理远程控制事件

(void)remoteControlReceivedWithEvent:(UIEvent *)event
{
// 根据事件的子类型(subtype) 来判断具体的事件类型, 并做出处理
switch (event.subtype) {
case UIEventSubtypeRemoteControlPlay:
case UIEventSubtypeRemoteControlPause: {
// 执行播放或暂停的相关操作 (锁屏界面和上拉快捷功能菜单处的播放按钮)
break;
}
case UIEventSubtypeRemoteControlPreviousTrack: {
// 执行上一曲的相关操作 (锁屏界面和上拉快捷功能菜单处的上一曲按钮)
break;
}
case UIEventSubtypeRemoteControlNextTrack: {
// 执行下一曲的相关操作 (锁屏界面和上拉快捷功能菜单处的下一曲按钮)
break;
}
case UIEventSubtypeRemoteControlTogglePlayPause: {
// 进行播放/暂停的相关操作 (耳机的播放/暂停按钮)
break;
}
default:
break;
}
}
iOS7.1后, 更新了远程控制事件的实现方式

相关方法的描述中, 已经说明, iOS7.1之后使用MPRemoteCommandCenter类来进行远程控制事件的相关处理, 因此可以不再使用上面所描述的三个方法. 官方文档对MPRemoteCommandCenter的描述如下:

MPRemoteCommandCenter

MPRemoteCommandCenter类提供了处理远程控制事件的对象, 包括由外部附件和系统传输控制发送的远程控制事件. 不需要自己创建该类的实例. 而是使用shareCommandCenter方法获取默认的命令中心(share command center)对象. share command center对象的属性包含了MPRemoteCommand对象(表示iOS支持的每种远程控件事件). 如果要对响应的事件特殊处理, 使用适当的MPRemoteCommand对象注册一个handler即可.

远程命令中心(remote command center)对象为许多不同类型的事件提供了命令(command)对象. 如果你的App不需要支持某些特定类型的事件, 可以通过设置其enabled属性为NO来禁用关联的MPRemoteCommand对象. 使用command对象注册一个handler, 以便让系统知道你的App已经做好了接收事件的准备. 只有当你的App是当前正在播放(Now Playing App)时才能接收到事件的传递.

项目中的代码实现 :

// 在需要处理远程控制事件的具体控制器或其它类中实现

(void)remoteControlEventHandler
{
// 直接使用sharedCommandCenter来获取MPRemoteCommandCenter的shared实例
MPRemoteCommandCenter *commandCenter = [MPRemoteCommandCenter sharedCommandCenter];

// 启用播放命令 (锁屏界面和上拉快捷功能菜单处的播放按钮触发的命令)
commandCenter.playCommand.enabled = YES;
// 为播放命令添加响应事件, 在点击后触发
[commandCenter.playCommand addTarget:self action:@selector(playAction:)];

// 播放, 暂停, 上下曲的命令默认都是启用状态, 即enabled默认为YES
// 为暂停, 上一曲, 下一曲分别添加对应的响应事件
[commandCenter.pauseCommand addTarget:self action:@selector(pauseAction:)];
[commandCenter.previousTrackCommand addTarget:self action:@selector(previousTrackAction:)];
[commandCenter.nextTrackCommand addTarget:self action:@selector(nextTrackAction:)];

// 启用耳机的播放/暂停命令 (耳机上的播放按钮触发的命令)
commandCenter.togglePlayPauseCommand.enabled = YES;
// 为耳机的按钮操作添加相关的响应事件
[commandCenter.togglePlayPauseCommand addTarget:self action:@selector(playOrPauseAction:)];
}
