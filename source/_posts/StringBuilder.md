---
title: Java StringBuilder란?
author: Hamin
date: 2020-08-17 21:11:30
tags: [Java]
categories:
- [Java]
thumbnail: /gallery/java.png
---

String은 불변(immutable) 객체이다.

Java 에서 String을 다루는 대표적인 클래스로 String, StringBuffer, StringBuilder가 있습니다.

<!--more-->

연산이 많지 않을 때는 위에 나열된 어떤 클래스를 사용하더라도 이슈가 발생할 가능성은 거의 없습니다.

그러나 연산횟수가 많아지거나 멀티스레드, Race Condition 등의 상황이 자주 발생한다면 각 클래스의 특징을 이해하고 상황에 맞는 적절한 클래스를 사용해야 합니다.

그 중에서 이 글에서는 StringBuilder에 대해 적어 보겠습니다.

~~~java
String str1 = "abc";
String str2 = "def";
~~~

2개의 String 객체가 있을 때, 만약 str1 + str2; 와 같은 연산을 하게 되면 새로운 String을 생성한다.

그래서 나온 것이 StringBuilder이다.

StringBuilder는 String과 문자열을 더할 때 새로운 객체를 생성하는 것이 아니라 기존의 데이터에 더하는 방식을 사용하기 때문에 속도도 빠르며 상대적으로 부하가 적다.

따라서 긴 문자열을 더하는 상황이 발생할 경우 StringBuilder를 적극적으로 사용하면 좋다.

## 사용법

~~~java
StringBuilder sb = new StringBuilder();
sb.append("ABC");
sb.append("DEF");
System.out.println(sb.toString());
~~~

StringBUilder에는 append()가 있는데, 이는 문자열을 더하는 역할을 한다.

만들어진 문자열을 출력하기 위해서는 StringBuilder의 인스턴스인 sb의 toString()을 부르자.

예제이므로 짧은 문자열을 더했지만, 빈번하게 긴 문자열을 더해야 할 경우 효과적이다.