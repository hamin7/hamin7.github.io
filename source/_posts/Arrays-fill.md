---
title: Java Arrays.fill() 메소드 이용하여 배열 초기화 하기
author: Hamin
date: 2020-08-18 19:12:44
tags: [Java]
categories:
- [Java]
thumbnail: /gallery/java.png
---

Array를 Arrays.fill() 메소드를 이용하여 배열의 사이즈만큼 지정한 값으로 한번에 초기화 할 수 있습니다.

또는 배열 내에서 구간을 지정하여 특정 값으로 초기화 할 수 있습니다.

<!--more-->

> 기본 채우기

~~~java
import java.util.Arrays;

public class MyClass {
    public static void main(String args[]) {
        int[] arr = new [] {1, 2, 3, 4, 5};
        Arrays.fill(arr, 100);
        System.out.println(Arrays.toString(arr));
        // [100, 100, 100, 100, 100]
    }
}
~~~

> 구간 지정하여 채우기

~~~java
import java.util.Arrays;

public class MyClass {
    public static void main(String args[]) {
        int[] arr = new int[] {1, 2, 3, 4, 5};
        Arrays.fill(arr, 2, 4, 100);
        System.out.println(Arrays.toString(arr));
        // [1, 2, 100, 100, 5]
    }
}
~~~

> 2차원 배열 채우기

~~~java
import java.util.Arrays;

public class MyClass {
    public static void main(String args[]) {
        int[][] arr = new int[3][5];
        for (int[] row: arr) {
            Arrays.fill(row, 10);
        }
        for (int[] row: arr) {
            System.out.println(Arrays.toString(row));
        }
        // [10, 10, 10, 10, 10]
        // [10, 10, 10, 10, 10]
        // [10, 10, 10, 10, 10]
    }
}
~~~

> 3차원 배열 채우기

~~~java
import java.util.Arrays;

public class MyClass {
    public static void main(String args[]) {
        int[][][] arr = new int[3][5][2];
        for (int[][] row2: arr) {
            for (int[] row: row2) {
                Arrays.fill(row, 10);
            }
        }
        for (int[][] row2: arr) {
            for (int[] row: row2) {
                System.out.print(Arrays.toString(row));
            }
            System.out.println();
        }
        // [10, 10][10, 10][10, 10][10, 10][10, 10]
        // [10, 10][10, 10][10, 10][10, 10][10, 10]
        // [10, 10][10, 10][10, 10][10, 10][10, 10]
    }
}
~~~

## References
- https://www.geeksforgeeks.org/arrays-fill-java-examples/
- https://www.tutorialspoint.com/java/util/arrays_fill_int.htm
- https://docs.oracle.com/javase/10/docs/api/java/util/Arrays.html