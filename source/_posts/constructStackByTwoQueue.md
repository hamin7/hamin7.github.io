---
title: 큐로 스택 구현하기
author: Hamin
date: 2020-10-21 15:42:11
tags: [DataStructure, CS]
categories: 
- [CS, DataStructure]
thumbnail: /gallery/dataStructure.png
---

두 개의 큐를 이용해 스택을 구현해보자.

두 개의 큐가 존재한다.
목적에 따라 메인 큐와 서브 큐로 생각해보자.

흐름은 아래와 같다.

<!--more-->

1. 메인큐에 item이 입력될 때. (입력 전)
2. 기존 메인큐에 존재하는 아이템들은 순서대로 서브큐에 담는다.
3. 메인큐에 item이 입력되고 서브큐에 있던 아이템들을 순서대로 메인큐에 다시 담는다.

{% asset_img 1.png %}

1이 입력될 때 기존에 메인큐에 있던 Item이 없기 때문에 그대로 입력된다.

{% asset_img 2.png %}

2가 입력될 때, 메인큐에 기존에 있던 item을 서브큐로 옮기고
메인큐에 2가 입력된 후, 서브큐의 item들을 메인큐로 옮긴다.

{% asset_img 3.png %}

3이 입력될 때, 기존에 있던 메인큐 item들을 서브큐로 옮기고
3이 입력된 후, 서브큐에 존재하는 item들을 다시 메인큐로 옮긴다.

