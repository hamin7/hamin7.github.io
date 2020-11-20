---
title: Stack vs Queue
author: Hamin
date: 2020-10-16 17:17:25
tags: [DataStructure, CS]
categories: 
- [CS, DataStructure]
thumbnail: /gallery/dataStructure.png
---

# 선형구조란

선형구조 (Linear Structure)는 데이터들이 일렬로 저장되어 있는 형태이다.
일렬로 저장하는 방식은 리스트와 각 데이터가 다음 데이터의 위치를 가지는 연결 리스트 두 가지 방식이 있다. 일렬로 쭉 저장되어 있는 데이터를 사용하는 방법은 리스트와 연결 리스트 외에 사용 방법에 따라 스택 (Stack), 큐 (Queue) 데크가 추가된다.

<!--more-->

# Stack

- 선형 자료구조의 일종으로 Last In First Out (LIFO).
- 즉, 나중에 들어간 원소가 먼저 나온다. 이것은 Stack의 가장 큰 특징.
- 차곡차곡 쌓이는 구조로 먼저 Stack에 들어가게 된 원소는 맨 바닥에 깔리게 된다.
- 그렇기 때문에 늦게 들어간 녀셕들은 그 위에 쌓이게 되고 호출 시 가장 위에 있는 녀석이 호출되는 구조이다.

# Queue

- 선형 자료구조의 일종으로 First In First Out (FIFO).
- 즉, 먼저 들어간 놈이 먼저 나온다. Stack과는 반대로 먼저 들어간 놈이 맨 앞에서 대기하고 있다가 먼저 나오게 되는 구조이다.
- 참고로 Java Collection에서 Queue는 인터페이스이다.
- 이를 구현하고 있는 Priority queue 등을 사용할 수 있다.
- 큐의 구현은 배열 (Array)을 이용하거나 순환 큐, 또는 연결 리스트 (Linked List)를 이용해도 된다.

# Stack은 언제 사용할까?

> 스택은 대표적으로 프로그램을 수행할 때 사용합니다
> Main 프로그램에서 함수 A를 호출하면 Main 프로그램 위에 함수 A가 쌓이고, 함수 A의 수행 중에 함수 B가 호출되면, 함수 A위에 함수 B가 스택처럼 쌓이게 된다.
> 함수 B의 실행이 완료되면 함수 A가 실행되고, 함수 A의 실행이 완료되면 주 프로그램의 실행이 시작된다 (LIFO)

# Queue는 언제 사용할까?

> 컴퓨터 안에 여러 개의 프로세스가 수행 중인데, 새로운 프로세스가 수행되어야 하는 경우 기존에 수행되던 프로세스 중에서 가장 먼저 메모리에 올라온 프로세스가 아웃되고 (실행), 새로운 프로세스를 메모리에 올리게 됩니다. 이 경우 운영체제는 현재 수행 중인 프로세스를 큐의 형태로 관리합니다.

> Windows 운영체제를 사용하는 컴퓨터에서 수행 중인 프로그램에 이벤트 (버튼 누르기, 윈도우 크기 조정, 메뉴 선택하기 등)가 발생하면 발생한 이벤트가 큐에 저장되고, 수행중인 프로그램이 큐에 저장된 것을 앞에서부터 읽어 와서 처리한다 (FIFO)

# Stack 사용 방법

- push : 스택에 자료 넣음
- pop : 스택에서 자료 뺌
- top : 스택의 가장 위에 있는 자료
- empty : 스택이 비어있는지 아닌지 확인
- size : 스택에 저장된 자료의 개수 확인
- C++ : STL의 stack 사용
- Java : java.util.Stack

# Queue 사용 방법

- push : 큐에 자료 넣음
- pop : 큐에서 자료 뺌
- front : 큐의 가장 앞에 있는 자료
- back : 큐의 가장 뒤에 있는 자료
- empty : 큐가 비어있는지 아닌지 확인
- size : 큐에 저장되어있는 자료 개수 확인
- C++ : STL의 queue 사용
- Java : java.util.Queue

# Deque 사용 방법

- 양 끝에서만 자료를 넣고 양 끝에서 뺄 수 있는 자료구조
- push_front : 큐에 자료를 넣음
- pop : 큐에서 자료를 뺌
- front : 큐의 가장 앞에 있는 자료
- back : 큐의 가장 뒤에 있는 자료
- empty : 큐가 비어있는지 아닌지 확인
- size : 큐에 저장되어있는 자료 개수 확인

# 스택과 큐의 java 코드 및 실행결과

~~~java
import java.util.Stack;
import java.util.Queue;

public class stackQueue {
    public static void main(String[] args) {
        Stack<String> st = new Stack<String>();
        Queue<String> q = new LinkedList<String>();

        st.push("0");
        st.push("1"); 
        st.push("2");

        q.offer("0");
        q.offer("1");
        q.offer("2");

        System.out.println("-- Stack --");
        while (!st.isEmpty()) {
            System.out.println(st.pop());
        }

        System.out.println("-- Queue --");
        while (!q.isEmpty()) {
            System.out.println(st.poll());
        }
    }
}
~~~

## 결과

~~~markdown
-- Stack --
2
1
0
-- Queue --
0
1
2
~~~

# 참고

## 스택 활용 문제

괄호 : https://www.acmicpc.net/problem/9012
쇠막대기 : https://www.acmicpc.net/problem/10799
에디터 : https://www.acmicpc.net/problem/1406

## 큐 활용 문제

조세퍼스 문제 : https://www.acmicpc.net/problem/1158

## 덱 구현 문제
https://www.acmicpc.net/problem/10866