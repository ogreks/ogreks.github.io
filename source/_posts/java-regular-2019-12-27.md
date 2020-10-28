---
title: java 正则表达式 (regular)
date: 2019-12-27 02:49:59
categories:
 - java
 - java 基础
tags:
 - java 正则
 - java regular
 - regular
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---

## Java 的 正则表达式使用

> 正则表达式，又称规则表达式.（英语：Regular Expression ,在代码中常简写 regex 、 regexp 或 RE）, 计算机科学的一个概念。正则表达式通常被用来检索、替换那些符合某个模式（规则）的文本。

Java 通过 ***java.util.regex*** 包提供正则表达式的功能

### 示例

Java 使用正则表达式匹配非常简单，我们以邮箱地址为例

```
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexDemo {
    public static void main(String[] args) {
//       Pattern类 正则表达式的编译表示。
        Pattern pattern = Pattern.compile("^[a-zA-Z0-9_!#$%&'*+/=?`{|}~^.-]+@[a-zA-Z0-9.-]+$");
        String[] emails = {"shiyanlou@shiyanlou.com", "shiyanlou"};
        for (String email :
                emails) {
//Matcher 通过解释Pattern对字符序列执行匹配操作的引擎
            Matcher matcher = pattern.matcher(email);
            System.out.println(email + "匹配结果：" + matcher.matches());
        }
    }
}
```
