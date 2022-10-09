---
title: "[Sort Algorithm] Insertion sort"
categories:
  - Algorithm
tags:
  - Sort
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-10-09
---

### 삽입정렬이란?

삽입정렬은 배열의 첫번째와 두번째 index를 비교하여 (오름차순 정렬일 경우) 더 작은 값을 앞으로 보낸다. <br>
그 다음에는 배열의 두번째와 세번째 index를 비교하여 세번째 index가 더 작다면 서로 위치를 바꾸고, 다시 첫번째 index와 비교하여 더 작은 값을 앞으로 보낸다. 그렇지 않다면 현재 단계의 프로세스를 중단하고 배열의 세번째와 네번째 index를 비교하는 단계로 넘어간다.<br>
이런식으로 배열의 마지막 index에 도달할때까지 계속 반복한다. <br><br>
예시 <br>
[37, 24, 14, 20, 13] 오름차순 정렬<br>
1차 : 37, 24 비교 & 서로 위치 교환 <br>
< 결과 : [24, 37, 14, 20, 13] > <br>
2차 : 37, 14 비교 & 서로 위치 교환, 24, 14 비교 & 서로 위치 교환 <br>
< 결과 : [14, 24, 37, 20, 13] > <br>
3차 : 37, 20 비교 & 서로 위치 교환, 24, 20 비교 & 서로 위치 교환, 14, 20 비교 & 위치 교환이 없으므로 3차 작업 종료 <br>
< 결과 : [14, 20, 24, 37, 13] > <br>
4차 : 37, 13 비교 & 서로 위치 교환, 24, 13 비교 & 서로 위치 교환, 20, 13 비교 & 서로 위치 교환, 14, 13 비교 & 서로 위치 교환<br>
< 결과 : [13, 14, 20, 24, 37] > <br><br>
삽입정렬은 최악의 경우엔, (n - 1) + (n -2) .... 1 = n(n-1)/2 번 연산을 수행하기 때문에 시간복잡도가 O(N^2)이다. <br>
그러나 최선의 경우엔, 배열을 한 번 순회만 하고 알고리즘이 종료되기 때문에 시간복잡도가 O(N)이 된다. <br>
알고리즘 구현이 비교적 간단하지만, 배열의 크기가 커질수록 계산속도가 기하급수적으로 느려지는 단점이 있다. 다만, 정렬하려는 배열이 이미 어느정도 정렬이 된 상태라면 버블정렬, 선택정렬보다는 좀 더 효율적이다. <br>

![Insertion-sort-example-300px](https://user-images.githubusercontent.com/69135840/194742999-8b24b67b-6f1a-4f42-8528-8923458a0323.gif)<br>
삽입정렬 예시 (출처 : [https://en.wikipedia.org/wiki/Insertion_sort](https://en.wikipedia.org/wiki/Insertion_sort)) <br>

### Java Code

```java
public class InsertionSort {
  public static void main(String[] args) {
    int[] arr = {1, 2, 10, 3, 16, 255, -1, -8, 23, 543, 1, 6, 5, 13};
    int[] result = sort(arr);
    for (int i = 0; i < arr.length; i++) {
      System.out.print(result[i] + ", ");
    }
  }

  public static int[] sort(int[] arr) {
    int length = arr.length - 1;
    for (int i = 0; i < length; i++) {
      int j = i;
      while (j >= 0 && arr[j] > arr[j + 1]) {
        swap(arr, arr[j], arr[j + 1], j);
        j--;
      }
    }
    return arr;
  }

  public static void swap(int[] arr, int firstNo, int secondNo, int index) {
    arr[index] = secondNo;
    arr[index + 1] = firstNo;
  }
}
```

<br>
![01](https://user-images.githubusercontent.com/69135840/194743211-2cc7c645-f8c6-4691-ad81-505ca2815059.png) <br>
삽입정렬 시작 전 <br><br>
![02](https://user-images.githubusercontent.com/69135840/194743212-9481f8e0-c965-41f9-8b6f-0526fe559c3b.png) <br>
삽입정렬 종료 후 <br><br>

### Github link

[https://github.com/kwonminho1992/Algorithm-practice](https://github.com/kwonminho1992/Algorithm-practice)
