---
title: "JAVA 공식문서 읽는 법"
categories:
  - Java Basic
tags:
  - java
  - java document
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-05-14
---

### 서론

자바로 개발을 하다보면 기본 라이브러리에서 제공하는 메서드 등을 사용할 때가 자주 생긴다. <br>
물론 간단한 정보는 구글링으로 충분히 해결할 수 있지만, 내가 필요로하는 구체적인 정보를 얻기엔 검색만으로는 한계가 있다. <br>
그래서 구글링으로 대충 어느 클래스, 메서드를 써야하는지 찾고, 자바 공식문서에서 구체적인 사용법을 찾아 볼 필요가 있다. <br>
그런데 초보자 입장에선 공식문서를 그냥 본다고 바로 이해할 수 있는게 아니며, 공식문서를 읽는 방법을 어느정도 배울 필요가 있다. <br>

### JAVA 공식문서에서 필요한 정보 찾아보기

숫자를 입력하면 콘솔창에 3자리마다 콤마(,)를 찍고, 숫자 앞에 KRW가 찍히도록 하는 간단한 프로그램을 만들어보자 <br>
(ex. 10000 입력 -> KRW 10,000 출력) <br>
물론 3자리마다 콤마를 찍는 로직을 직접 구현할 수도 있겠지만, 복잡하고 비효율적이기 때문에 기본 라이브러리에 있는 함수를 쓰는 것이 더 효율적인 방법일 것이다. <br>
일단, 어떤 클래스, 메서드를 써야 로직을 구현할 수 있는지 모르므로, 구글에서 적절한 검색어로 검색을 해본다. <br>
(검색어 예시 : format number with commas java) <br>

![01](https://user-images.githubusercontent.com/69135840/168422112-b276d970-0732-4723-9516-63dcb7825e50.png) <br>
가장 상단에 나온 링크를 클릭해보니 아래와 같이 해보면 3자리마다 콤마를 찍을 수 있다고 한다. <br>
하지만 #과 ,0은 무슨 역할을 하는건지 숫자 앞에 KRW는 어떻게 붙일 수 있는 것인지에 대해서까지는 알려주지 않는다. <br>
![02](https://user-images.githubusercontent.com/69135840/168459401-b06d8d09-b009-45ce-a043-9804ce607855.png) <br>
[자바공식문서](https://docs.oracle.com/javase/8/docs/api/) 에 접속하여 일단 DecimalFormat class로 들어가본 후, 생성자부분과 format() method에 대해 좀 더 찾아보자 <br>

```java
// 공식문서에서 찾아볼 코드
DecimalFormat formatter = new DecimalFormat("#,###.00");
System.out.println(formatter.format(amount));
```

![03](https://user-images.githubusercontent.com/69135840/168460039-356d38b9-7a78-45cd-a2ab-8cc74775e8f5.png) <br>
공식문서의 좌측 하단 클래스 영역에서 DecimalFormat 클래스를 검색하여 들어간 다음, <br><br>
![04](https://user-images.githubusercontent.com/69135840/168460043-d20ec315-d78d-4368-b62e-c9880a6380cf.png) <br>
생성자 (Constructor) 부분을 찾아봤다. 그 중 파라미터로 String 타입을 받는 두번째 생성자에 대해 자세히 살펴보자 <br><br>
![05](https://user-images.githubusercontent.com/69135840/168460220-34343cde-52cd-4596-af28-95137249c6c9.png) <br>
패턴을 설정하라 나오는데, 구체적으로 설정할 수 있는 패턴에 대해선 스크롤을 맨 위로 올린 다음, 다시 천천히 내리다보면 나온다. <br><br>
![06](https://user-images.githubusercontent.com/69135840/168460346-170fbd23-3feb-4911-9330-1bab03e8912e.png) <br>
천천히 읽어보니 "###,###,###,###" 이런 식으로 적으면 3자리 마다 콤마를 찍을 수 있으며, "#,###"라고 해도 숫자의 크기와 상관없이 같은 결과를 출력할 수 있다고 한다 (밑줄 참고) <br><br>
![07](https://user-images.githubusercontent.com/69135840/168460480-437a054c-aad6-4170-8d96-a36be1447228.png)<br>
format() 메서드에 대해서 찾아보니 3개의 파라미터를 가지고 있다는걸 알 수 있었다 <br>
long number : formatting 할 숫자 <br>
StringBuffer result : 붙이고 싶은 텍스트 <br>
FieldPosition fieldPosition : 이것은 무엇인지 아직 잘 모르겠다. 그래서 FiledPosition 클래스를 더 찾아보았다. <br>
![08](https://user-images.githubusercontent.com/69135840/168461784-32c9435a-1a8d-4cf7-ab9b-fb809117b6af.png) <br>
생성자 부분을 찾아보았는데, 그래도 여전히 이것이 어떤 기능을 할지는 감이 잡히지 않았다. <br>
그래서 See Also: 에 있는 <br>
NumberFormat.INTEGER_FIELD, NumberFormat.FRACTION_FIELD, DateFormat.YEAR_FIELD, DateFormat.MONTH_FIELD를 하나씩 다 넣어서 객체를 생성해봤는데,
결과에 영향을 끼치진 않았다. FieldPosition 파라미터가 무슨 역할을 하는지에 대해선 공식문서에서 주는 정보만으로는 이해하기 어려웠지만, 아무튼 원하는 정보는 충분히 얻을 수 있었다. <br>

```java
// 최종코드
	public static void main(String[] args) {
		int num = 10000; // formatting할 숫자
		DecimalFormat formatter = new DecimalFormat("#,###"); // 3자리마다 콤마 찍기

		StringBuffer sb = new StringBuffer("KRW "); // 숫자 앞에 붙일 텍스트
		FieldPosition fp = new FieldPosition(NumberFormat.INTEGER_FIELD);
		System.out.println(formatter.format(num, sb, fp));
	}

  //출력결과
  KRW 10,000
```
