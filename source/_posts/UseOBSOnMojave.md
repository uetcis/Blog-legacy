---
title: 在 macOS Mojave 下使用 OBS 直播的正确方法
date: 2018-07-28 15:22:08
tags: 
- Live
- OBS
categories: Tech
---
请在命令行中使用 `/Applications/OBS.app/Contents/MacOS/OBS; exit` 来启动 OBS， 否则会无法捕获声音/摄像头。

官方确认会在下一版本中解决此问题，故上述方法为临时解决方案。

具体讨论请见 [Bug Report: Mojave 10.14 Beta (18A336e) and audio not working](https://obsproject.com/forum/threads/mojave-10-14-beta-18a336e-and-audio-not-working.91782/#post-366057)
