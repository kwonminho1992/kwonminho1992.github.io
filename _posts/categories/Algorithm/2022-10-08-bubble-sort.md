---
title: "[Sort Algorithm] Bubble sort"
categories:
  - Algorithm
tags:
  - Sort
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-10-08
---

### 버블정렬이란?

버블정렬은 배열의 처음부터 끝까지 순회하면서 두 수를 서로 교환하는 방식으로 정렬을 진행한다. <br>
이렇게 한번 배열을 처음부터 끝까지 돌면, 배열의 마지막 index에 어떤 수가 와야하는지 결정된다. <br>
그 다음에 다시 배열을 처음부터 돌되, 마지막 index에 올 수가 정해졌으므로 이번에는 배열의 마지막 index에서 1칸 앞까지만 돈다. <br>
이런식으로 배열의 첫번째 index에 도달할때까지 계속 반복한다. <br>
말로만 설명하면 어렵기 때문에 예시를 하나 들겠다. <br>
[1, 4, 2, 3] 라는 배열이 있고, 오름차순으로 정렬한다 가정하면,<br><br>
1차 : 1, 4 비교 (1 < 4 이므로 교환 X), 4, 2 비교 (2 < 4 이므로 교환), 4, 3 비교( 3 < 4 이므로 교환) <br>
< 결과 : [1, 2, 3, 4] > <br>
2차 : 1, 2 비교 (1 < 2 이므로 교환 X), 2, 3 비교 (2 < 3 이므로 교환 X) <br>
< 결과 : [1, 2, 3, 4] > <br>
3차 : 1, 2 비교 (1 < 2 이므로 교환 X) <br>
< 결과 : [1, 2, 3, 4] > <br><br>
이렇게 되겠다. 이미 1차에서 정렬이 완료되었지만, 버블정렬은 배열의 첫번째 index에 도달할 때까지 순회해야하므로 3차까지 진행되었다. <br>
그렇기 때문에 버블정렬은 구현하기가 쉽지만, 비효율적이라 잘 쓰이지 않는다고 한다. 하지만, 정렬 알고리즘 중에서 가장 기초적인 것이므로 알아둘 필요는 있을 것이다. <br>
한편, (n - 1) + (n -2) .... 1 = n(n-1)/2 번 연산을 수행하기 때문에 버블정렬의 시간복잡도는 O(N^2)가 되겠다.
(최초 정렬상태가 최상, 최악, 평균인 경우에 상관없이 비교는 끝까지 진행되므로 어떤 경우에건 시간복잡도가 O(N^2) 이다.)<br><br>

![Bubble-sort-example-300px](https://user-images.githubusercontent.com/69135840/194734668-52fc1057-b648-49c3-9e50-2756233b3deb.gif)<br>
버블정렬 예시 (출처 : [https://en.wikipedia.org/wiki/Bubble_sort](https://en.wikipedia.org/wiki/Bubble_sort)) <br>

### Java Code

```java
public class BubbleSort {
  public static void main(String[] args) {
    int[] arr = {1, 2, 10, 3, 16, 255, -1, -8, 23, 543, 1, 6, 5, 13};
    int[] result = sort(arr);
    for (int i = 0; i < arr.length; i++) {
      System.out.print(result[i] + ", ");
    }
  }

  public static int[] sort(int[] arr) {
    int length = arr.length;
    int innerLength = length - 1;
    for (int i = 0; i < length; i++) {
      for (int j = 0; j < innerLength; j++) {
        swap(arr, arr[j], arr[j + 1], j);
      }
      innerLength--;
    }
    return arr;
  }

  private static void swap(int[] arr, int firstNo, int secondNo, int index) {
    if (secondNo < firstNo) {
      arr[index] = secondNo;
      arr[index + 1] = firstNo;
    }
  }
}
```

<br>
![01](https://user-images.githubusercontent.com/69135840/194698453-97968a04-7c77-4d20-bb5d-0bb1d4ecc550.png) <br>
버블정렬 시작 전 <br><br>
![02](https://user-images.githubusercontent.com/69135840/194698456-7b0d005a-6798-4f4c-8314-ae00196e7c92.png) <br>
버블정렬 종료 후 <br><br>
