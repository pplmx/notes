---
categories:
    - python
date: 2017-12-14T10:13:45Z
description: spider netease music by python
keywords: spider, python
lastmod: 2026-08-14T00:00:00Z
tags:
    - spider
    - python
    - music
title: 抓取网易云音乐歌单
---



> **Note (2026-08-14):** 本文针对 2017-2020 年代的 music.163.com 页面结构编写。当日复核:`https://music.163.com/discover/playlist` 仍以服务端渲染返回 200,`ul#m-pl-container`、`a.msk`、`span.nb` 等选择器均仍存在,无需登录,原脚本基本可用;但网易云前端随时可能改版或加反爬,商业用途请改用[官方 API](https://neteasecloudmusicapi.js.org/)或 NeteaseCloudMusicApi。另注意当前歌单图片 `src` 返回的是 `http://` 链接。

# Something needed before action

    需要使用到lxml和beautifulsoup,都可以使用pip安装

<!-- more -->

# In action

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Created by PyCharm
# @author  : mystic
# @date    : 2017/11/26 9:39
"""
    抓取网易云音乐
"""
import urllib.request

from bs4 import BeautifulSoup


def get_html(url, headers):
    req = urllib.request.Request(url, headers=headers)
    with urllib.request.urlopen(req) as resp:
        content = resp.read().decode('utf-8')
    return content


def parse_html(html):
    host = 'https://music.163.com'
    soup = BeautifulSoup(html, 'lxml')
    # 歌单图片[src]
    playlist_img = soup.select('ul#m-pl-container li div img')
    # 歌单名称和链接[title|href]
    playlist_name = soup.select('ul#m-pl-container li div a.msk')
    # 歌单播放量[text]
    playlist_views = soup.select('ul#m-pl-container li div.bottom span.nb')
    # 歌单创建者[title|href]
    playlist_creator = soup.select('ul#m-pl-container li p > span + a')
    for i in range(len(playlist_creator)):
        print('歌单封面: ', playlist_img[i]['src'])
        print('歌单名称: ', playlist_name[i]['title'])
        print('歌单链接: ', host + playlist_name[i]['href'])
        print('歌单播放量: ', playlist_views[i].text)
        print('歌单创建者: ', playlist_creator[i]['title'])
        print('创建者主页: ', host + playlist_creator[i]['href'], '\n')


if __name__ == '__main__':
    spider_url = 'https://music.163.com/discover/playlist'
    result = get_html(spider_url, headers={
        'User-Agent': 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)',
        'Host': 'music.163.com'
    })
    parse_html(result)

```

# Something worth noting

    1.python版本: 3.6.3
    2.可以结合前一篇,做个歌词分析

[Github Source Code](https://github.com/pplmx/data_mining.git)
