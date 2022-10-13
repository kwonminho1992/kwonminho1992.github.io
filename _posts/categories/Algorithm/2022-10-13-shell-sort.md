---
title: "[Sort Algorithm] Shell sort"
categories:
  - Algorithm
tags:
  - Sort
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-10-13
---

### 셸정렬이란?

[삽입정렬](https://kwonminho1992.github.io/algorithm/insertion-sort/)은 정렬이 전혀 안된 상태에서는 시간복잡도가 O(N^2)로 비효율적이지만, 정렬하려는 배열이 이미 어느정도 정렬이 된 상태라면 상당히 빠른 속도로 정렬을 완료할 수 있다. 그렇다면 배열을 어느정도 정렬된 상태로 만들고 삽입정렬을 실행한다면 빠르게 정렬을 할 수 있지 않을까? 이러한 생각에서 나온게 셸정렬이다. <br>
참고로 셸정렬이라는 이름은 이 정렬 알고리즘이 도널드 셸(Donald Shell) 이라는 미국의 컴퓨터 공학자에 의해 1959년 처음 제안되었기 때문에 붙은 이름이다.<br><br>
셸정렬은 정렬하려는 배열의 요소들을 여러 그룹으로 나누어 각각 삽입정렬을 수행한 뒤 그것을 합치는 것을 반복하여 정렬을 완료한다. <br>
예를 들어 요소가 8개 짜리인 배열이 있다면, 4칸씩 떨어진 [1번째, 5번째], [2번째, 6번째], [3번째, 7번째], [4번째, 8번째] 이렇게 4개의 배열로 나누어 각각의 배열을 삽입정렬한다.<br>
그 다음에는 간격을 2칸으로 좁혀서 [1번째, 3번째, 5번째, 7번째], [2번째, 4번째, 6번째, 8번째] 이렇게 2개의 배열로 나누어 각각의 배열을 삽입정렬한다. <br>
이렇게 배열을 어느정도 정렬한 다음엔 1~8번째 요소 전체를 삽입정렬하여 정렬을 마무리한다. <br>
여기서는 간격을 4칸, 2칸, 1칸 으로 하였지만, 몇칸으로 하건 상관없다. 다만, 정렬이 진행될수록 칸 간격이 좁아져야한다. <br>
그렇다면 간격을 어떻게 해야 정렬이 빨라지는가 하는 질문이 생길 것이다. 여기에 정답은 없다. 하지만 여러 사람들이 적절하게 간격을 나누는 방법을 고안하였다. 이것을 [Gap sequences](https://en.wikipedia.org/wiki/Shellsort#Gap_sequences)라고 한다. <br>
Gap sequence에 따라 달라지지만, 셸정렬의 시간복잡도는 평균적으로 O(N^1.3)~O(N^1.5) 정도 된다고 한다. <br>

### Java Code

```java
public class ShellSort {
  public static void main(String[] args) {
    int[] arr = {12, 954, 1, 977, 34, 990, 100, 45, 97, 2, 41, 96, 105, 204, 2, 33, 6, 32, 423, 421,
        399, 120, 126, 101, 107, 3, 999, 23};
    sort(arr);
  }

  public static void sort(int[] arr) {
    int length = arr.length;
    for (int gap = length / 2; gap >= 1; gap /= 2) {
      for (int i = gap; i < length; i++) {
        int j = i - gap;
        while (j >= 0 && j < length && arr[j] > arr[j + gap]) {
          int temp = arr[j];
          arr[j] = arr[j + gap];
          arr[j + gap] = temp;
          j = j - gap;
        }
      }

      // gap에 따른 현재 정렬 상태 출력
      System.out.print("gap : " + gap + " - [");
      for (int i = 0; i < length; i++) {
        System.out.print(arr[i] + ", ");
      }
      System.out.print("]\n");
    }
  }
}

/*result
gap : 14 - [2, 33, 1, 32, 34, 421, 100, 45, 97, 2, 41, 3, 105, 23, 12, 954, 6, 977, 423, 990, 399, 120, 126, 101, 107, 96, 999, 204]
gap : 7 - [2, 33, 1, 32, 3, 105, 23, 12, 97, 2, 41, 34, 421, 100, 45, 126, 6, 107, 96, 990, 204, 120, 954, 101, 977, 423, 999, 399]
gap : 3 - [2, 3, 1, 2, 6, 34, 23, 12, 45, 32, 33, 97, 96, 41, 101, 120, 100, 105, 126, 423, 107, 399, 954, 204, 421, 990, 999, 977]
gap : 1 - [1, 2, 2, 3, 6, 12, 23, 32, 33, 34, 41, 45, 96, 97, 100, 101, 105, 107, 120, 126, 204, 399, 421, 423, 954, 977, 990, 999]
*/
```

### Github link

[https://github.com/kwonminho1992/Algorithm-practice](https://github.com/kwonminho1992/Algorithm-practice)
