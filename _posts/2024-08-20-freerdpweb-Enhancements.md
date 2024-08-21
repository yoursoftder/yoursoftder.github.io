---
layout: post
title:  "FreeRDPWebConnect_Beta2 fix Enhancements"
color:  blue
width:   3
height:  1
date:   2024-08-19
categories: javascript Code
excerpt_separator: <!--more-->
---

该项目在 FreeRDPWebConnect_Beta 软件的基础上进行了改进，增加了多个新功能以提升可用性和功能性。
<!--more-->
This project builds upon the FreeRDPWebConnect_Beta software, adding several new features to enhance usability and functionality.

### Features:

## Reconnection Button:
Added a button to reconnect after network disconnections, allowing unlimited reconnections. Also introduced remote clipboard functionality.

## Cross-Screen Text Sending:
Supports sending text to the remote host across screens. Default and custom interfaces are supported, with default recommended for non-experts.

## Homepage Modification:
The original index.html is now disguised. Visiting the homepage will redirect to a video website. The real entry point is http(s)://ip:port/rdp.html. After opening the real entry point, click the hidden text at the top-left corner (may not be visible on a black background, but is clickable) to reveal the user input field.



### 功能

## 断网重连按钮：
增加了一个按钮，用于在网络断开后重新连接，支持无限制重新连接，并增加了远程剪切板功能。

## 跨屏幕发文字：
支持跨屏幕向远程主机发送文字。支持默认和自定义接口，建议非专业人士使用默认接口。

## 首页修改：
原始的 index.html 被伪装。访问首页将重定向到一个观看视频的网站。真实入口为 http(s)://ip:port/rdp.html。打开真实入口后，点击最左上角的隐藏文字（背景为黑色时可能看不到，但可以点击）才会出现用户输入框。

### eg. 图示
# Cross-Screen Text Sending
![Cross-Screen Text Sending](https://github.com/yoursoftder/yoursoftder.github.io/blob/main/img/2024-08-21_16-17-09rdp.png?raw=true)
 

[you can download here](https://github.com/yoursoftder/yoursoftder.github.io/releases/download/main/FreeRDPWebConnect_Beta_fix.exe)

