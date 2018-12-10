---
layout: post
title:  "AVPlayer PreferredForwardBufferDuration"
date:   2018-12-10 17:06:48 +0800
categories: jekyll update
---

`AVPlayerItem.preferredForwardBufferDuration`
  
> Setting it to something low like five seconds will buffer the minimum amount that AVFoundation thinks you need to get started.
>
> ---WWDC 2016 Session 503 [Advances in AVFoundation Playback]

按照官方文档和WWDC上的说明，该参数指播放器应缓存时长。

然而，实际情况是，该参数无效，即使设置了5秒，实际缓存时长有时会超过5秒。

针对该情况，这里还有相关讨论[AVPlayerItem buffer duration not respected]，经过测试，里面提到的方法均无效。

所以，通过该方法无法实现网络视频的预加载。


[Advances in AVFoundation Playback]: https://developer.apple.com/videos/play/wwdc2016/503/
[AVPlayerItem buffer duration not respected]: https://forums.developer.apple.com/thread/63435
