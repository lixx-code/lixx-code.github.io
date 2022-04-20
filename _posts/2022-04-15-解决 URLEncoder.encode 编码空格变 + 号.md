---
layout: post
title: "解决 URLEncoder.encode 编码空格变 + 号"
subtitle: "使用org.springframework.web.util.UriUtils;"
date: 2022-4-15 11:26:53
author: "li.xx"
tags: 
  - JAVA
---

jdk自带的URL编码工具类 `URLEncoder` 在对字符串进行URI编码的时候，会把空格编码为 `+` 号。
空格的URI编码其实是：`%20`
搜素引擎上不少人都遇到这个问题，哀声一片。
解决办法大都是对编码后的字符串，进行 `+` 号替换为 `%20`。总感觉这种方式不`优雅`。

## 使用spring提供的 `UriUtils` 来代替URLEncoder进行编码

> spring提供了很多的这种`Utils`

```java
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

import org.springframework.web.util.UriUtils;

public class MainTest {
	public static void main(String[] args) throws UnsupportedEncodingException {
		String content = "Hello <springboot中文社区>";
		
		String result = URLEncoder.encode(content, "utf-8");
		System.out.println("URLEncoder编码：	" + result);
		
		result = UriUtils.encode(content, "utf-8");
		System.out.println("UriUtils编码：	" + result);
	}
}
```

输出结果

```
URLEncoder编码：Hello+%3Cspringboot%E4%B8%AD%E6%96%87%E7%A4%BE%E5%8C%BA%3E
UriUtils编码：Hello%20%3Cspringboot%E4%B8%AD%E6%96%87%E7%A4%BE%E5%8C%BA%3E
```