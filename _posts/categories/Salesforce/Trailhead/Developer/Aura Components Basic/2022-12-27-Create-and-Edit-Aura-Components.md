---
title: "[Salesforce Trailhead] Aura Components Basic(1) - Create and Edit Aura Components"
categories:
  - Salesforce Trailhead
tags:
  - Salesforce
  - Trailhead
  - Aura Components Basic
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-12-27
---

### 들어가며

이 포스트는 세일즈포스 트레일헤드 中 [Aura Components Basics](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics) 모듈의 [Create and Edit Aura Components](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_create)를 설명한다.참고로 해당 모듈은 [Developer Intermediate](https://trailhead.salesforce.com/content/learn/trails/force_com_dev_intermediate) 트레일의 하위 모듈이다. <br><br>

일단 [Create and Edit Aura Components](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_create)에서 요구하는 과제는 아래와 같다.<br>
![00](https://user-images.githubusercontent.com/69135840/209623860-aead3f9e-254b-4b93-8c50-e4aa63d3a9f4.png)<br><br>
그리고 완성시 개략적인 구조는 아래와 같다.<br>
![01](https://user-images.githubusercontent.com/69135840/209622920-f104df58-3489-430e-8ee4-03833455f699.png)<br><br>
일단, executer는 Lightning Application으로, 라이트닝 컴포넌트를 화면에 출력해주는 기능을 한다. 그리고 camping은 캠핑에 필요한 모든 하위 라이트닝 컴포넌트들을 한 곳에 모아놓는 상위 라이트닝 컴포넌트의 역할을 한다. campingHeader는 헤더에 관련된 라이트닝 컴포넌트, campingList는 캠핑용품 리스트를 보여주는 라이트닝 컴포넌트이며 camping의 하위 라이트닝 컴포넌트이다.<br><br>

### executer (Lightning Application) 만들기

Trailhead Playground에서 Developer Console을 열고, File -> New -> Lightning Application을 클릭한 뒤, Name에 executer라 적은 후, submit버튼을 누른다. (이름은 꼭 executer로 할 필요는 없다)<br>
![02](https://user-images.githubusercontent.com/69135840/209629106-b9e57053-f074-4078-932a-77b617cd9f0b.png)<br>
스크린샷처럼 빨간박스 쳐진 곳(APPLICATION)을 더블클릭하여 executer.app을 생성한다. 그리고 아래와 같이 코드를 작성한다. 빨간박스 위의 버튼 (Update Preview / Preview)를 클릭하면 화면이 뜰 것이다. 물론 지금은 라이트닝 컴포넌트 코드를 작성한게 아무것도 없기 때문에 화면에 출력되는 내용은 아무것도 없을 것이다.

```html
<!-- Ligthning Application : Preview 버튼을 통해 화면을 띄울 수 있음 -->
<aura:application >
    <c:camping/> <!-- 화면에 띄우려는 라이트닝 컴포넌트 : camping 라이트닝 컴포넌트 -->
</aura:application>
```

### camping 라이트닝 컴포넌트 만들기

이제 화면에 띄울 내용을 구성해보자.<br>
File -> New -> Lightning Component를 클릭한 뒤, camping이란 이름으로 라이트닝 컴포넌트를 생성한다. 그리고 COMPONENT버튼을 더블클릭하여, camping.cmp를 생성한다.<br>
참고로 세일즈포스에선 component가 html, style이 css, controller & helper가 javascript의 역할을 한다. controller는 component의 로직을 전체적으로 다루는 역할로 쓰이고, helper는 구체적인 로직 하나하나를 다룬다.<br>
![03](https://user-images.githubusercontent.com/69135840/209632171-f89ae28e-d797-4c02-bdbb-1626ed22939e.png)<br><br>
코드는 아래와 같이 작성한다.
```html
<!-- camping은 campingHeader와 campingList를 종합하는 상위 라이트닝 컴포넌트이므로, <aura:component> 안에 <c:campingHeader/>, <c:campingList/>가 들어간다. -->
<aura:component >
    <c:campingHeader/> 
    <c:campingList/>
</aura:component>
``` 

### campingHeader 라이트닝 컴포넌트 만들기
camping의 두 하위 component 중 먼저 campingHeader를 만들어보자.<br>
File -> New -> Lightning Component를 클릭한 뒤, campingHeader이란 이름으로 라이트닝 컴포넌트를 생성한다. 그리고 COMPONENT버튼을 더블클릭하여, campingHeader.cmp를 생성한다.
코드는 아래와 같이 작성한다.
```html
<aura:component >
	<h1><label>Camping List</label></h1>
</aura:component>
``` 
그리고 과제에선 폰트를 18px로 만들라고 요구하므로, STYLE버튼을 더블클릭하여 campingHeader.css를 생성한다. 이 파일은 campingHeader.cmp에서 유효한 스타일시트 파일이다.
코드는 아래와 같이 작성한다.
```css
h1.THIS {
    font-size: 18px;
}
``` 
### campingList 라이트닝 컴포넌트 만들기
File -> New -> Lightning Component를 클릭한 뒤, campingList이란 이름으로 라이트닝 컴포넌트를 생성한다. 그리고 COMPONENT버튼을 더블클릭하여, campingList.cmp를 생성한다.<br>
코드는 아래와 같이 작성한다.
```html
<aura:component >
    <ol>
        <li>Bug Spray</li>
        <li>Bear Repellant</li>
        <li>Goat Food</li>
    </ol>
</aura:component>
``` 
이제 executer로 돌아가 Update Preview/Preview 버튼을 클릭한다.<br>
![04](https://user-images.githubusercontent.com/69135840/209634125-5d4e2779-56cb-40ec-9da7-b0f6f1558e44.png)<br><br>
그러면 아래와 같이 화면이 뜰 것이다. <br>
![05](https://user-images.githubusercontent.com/69135840/209634504-a186c296-65c5-49af-b288-423dc6a41f45.png)<br>