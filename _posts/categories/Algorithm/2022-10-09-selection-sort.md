---
title: "[Sort Algorithm] Selection sort"
categories:
  - Algorithm
tags:
  - Sort
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-10-09
---

### 선택정렬이란?

선택정렬은 배열의 처음부터 끝까지 순회하면서 최소값(or 최대값)을 찾아내는 방식으로 진행된다. <br>
이렇게 한번 배열을 처음부터 끝까지 돌면, 배열의 첫번째 index에 어떤 수가 와야하는지 결정될 것이다. <br>
그 다음에는 배열의 두번째 index부터 끝까지 순회하면서 최소값(or 최대값)을 찾아내고, 배열의 두번째 index에 올 값을 정한다. <br>
이런식으로 배열의 마지막 index에 도달할때까지 계속 반복한다. <br><br>
예시 <br>
[37, 24, 32, 14, 13] 오름차순 정렬<br>
1차 : 37부터 13까지 순회, 최소값 : 13 <br>
< 결과 : [13, 24, 32, 14, 37] > <br>
2차 : 24부터 37까지 순회, 최소값 : 14 <br>
< 결과 : [13, 14, 32, 24, 37] > <br>
3차 : 32부터 37까지 순회, 최소값 : 24 <br>
< 결과 : [13, 14, 24, 32, 37] > <br>
4차 : 32부터 37까지 순회, 최소값 : 32 <br>
< 결과 : [13, 14, 24, 32, 37] > <br><br>
선택정렬은 버블정렬처럼 중간에 정렬이 끝나도 일단 끝까지 순회하기 때문에 정렬해야하는 배열의 크기가 커질 수록 소요시간이 기하급수적으로 늘어난다. <br>
선택정렬은 (n - 1) + (n -2) .... 1 = n(n-1)/2 번 연산을 수행하기 때문에 시간복잡도는 O(N^2)가 되겠다.
(최초 정렬상태가 최상, 최악, 평균인 경우에 상관없이 비교는 끝까지 진행되므로 어떤 경우에건 시간복잡도가 O(N^2) 이다.)<br><br>

![Selection-Sort-Animation](https://user-images.githubusercontent.com/69135840/194734669-9fe20914-4b73-45df-babc-77c0d68a3dc3.gif)<br>
선택정렬 예시 (출처 : [https://en.wikipedia.org/wiki/Selection_sort](https://en.wikipedia.org/wiki/Selection_sort)) <br>

### Java Code

```java
public class SelectionSort {
  public static void main(String[] args) {
    int[] arr = {1, 2, 10, 3, 16, 255, -1, -8, 23, 543, 1, 6, 5, 13};
    int[] result = sort(arr);
    for (int i = 0; i < arr.length; i++) {
      System.out.print(result[i] + ", ");
    }
  }

  public static int[] sort(int[] arr) {
    int length = arr.length;
    for (int i = 0; i < length; i++) {
      int min = arr[i];
      int j = i;
      int index = i;
      for (; j < length; j++) {
        if (arr[j] < min) {
          min = arr[j];
          index = j;
        }
      }
      arr[index] = arr[i];
      arr[i] = min;
    }
    return arr;
  }
}
```

<br>
![01](https://user-images.githubusercontent.com/69135840/194734890-dee8b7bc-105e-40ec-96c7-4d7d66a8e374.png) <br>
선택정렬 시작 전 <br><br>
![02](https://user-images.githubusercontent.com/69135840/194734891-6622d10c-2019-4598-b2e0-58c1fc62e662.png)<br>
선택정렬 종료 후 <br><br>

### Github link

[https://github.com/kwonminho1992/Algorithm-practice](https://github.com/kwonminho1992/Algorithm-practice)
