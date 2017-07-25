---
layout: post
title: 手机抓包
tags:
- 抓包
categories: debug
---

## fiddler
fiddler 只能抓取http/https的包
1. 手机与电脑处于同一个无线网中，手机设置代理，代理的ip地址为电脑的ip地址。
2. fiddler-tools-fiddler options -connections - 勾选allow remote computers to connect。
(可以通过手机usb共享网络给电脑，手机设置代理电脑的ip的地址)

## wireshark
wireshark 可以抓取socket的包
1. 电脑设置wifi热点，手机连上电脑设置的wifi
2. 在wireshark中选择监听WLAN
