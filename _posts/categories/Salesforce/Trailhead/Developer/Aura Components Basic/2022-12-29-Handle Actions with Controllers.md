---
title: "[Salesforce Trailhead] Aura Components Basic(3) - Handle Actions with Controllers"
categories:
  - Salesforce Trailhead
tags:
  - Salesforce
  - Trailhead
  - Aura Components Basic
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-12-29
---

### 들어가며

이 포스트는 세일즈포스 트레일헤드 中 [Aura Components Basics](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics) 모듈의 [Handle Actions with Controllers](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_controllers)를 설명한다.참고로 해당 모듈은 [Developer Intermediate](https://trailhead.salesforce.com/content/learn/trails/force_com_dev_intermediate) 트레일의 하위 모듈이다. <br><br>

### controller
먼저 요구하는 과제는 아래와 같다. <br>
![00](https://user-images.githubusercontent.com/69135840/209918246-d8721d3f-9adf-4f62-aa66-b828de429360.png)<br><br>
Packed! 라는 이름의 버튼을 누르면 Packed__c field의 값이 true가 되고, 버튼은 더이상 누를 수 없도록 막아야 한다.<br>
![01](https://user-images.githubusercontent.com/69135840/209918250-560fd792-18da-46a1-a8b5-c76ec4bf5f45.png)<br><br>
이를 위해 위의 스크린샷처럼 컴포넌트에 버튼을 추가하고, 컨트롤러를 만들어서 버튼이 클릭되는 이벤트가 발생하면 무엇을 할 것인지(Packed__c field의 값을 true로, 버튼은 클릭 불가 상태로)에 대한 메서드를 작성하면 된다.<br>
먼저 컴포넌트의 코드는 아래와 같다.
```html
<!-- campingListItem.cmp -->
<aura:component >
    <!--Camping Item object-->
    <aura:attribute name="item" type="Camping_Item__c" required="true" default=""/>
    <!-- name은 프로그램 상에서 쓰이는 변수 이름이라 생각하면 좋을 것 같다. -->
    <!--Camping Item fields-->
    <p>
        Name: {!v.item.Name} <br/>
        <!-- item attribute의 Name field 출력 -->
        Price : <lightning:formattedNumber value="{!v.item.Price__c}" style= "currency"/><br/>
        <!-- item attribute의 Price__c field 출력 
        (숫자로 표시되며, currency style 덕에 통화기호도 같이 표시되므로 돈과 관련된 정보임을 나타낼 수 있음) -->
        Quantity : <lightning:formattedNumber value="{!v.item.Quantity__c}"/><br/>
        <!-- item attribute의 Quantity__c field 출력 -->
        <lightning:input type="toggle"
                         label="Packed?"
                         name="Packed"
                         checked="{!v.item.Packed__c}"/>
        <!-- item attribute의 Packed__c field 출력 -->
    </p>
    <lightning:button label="Packed!"
            onclick="{!c.packItem}"/>
    <!-- Packed! 버튼 만들기, 버튼을 클릭하면 컨트롤러에서 pakItem 메서드가 실행될 것임 -->
</aura:component>
```
<br>이제 컨트롤러에 packItem이란 이름의 메서드를 만들고, 그 안에 버튼이 클릭되었을 때 실행할 코드를 작성한다.<br>
컨트롤러를 만드는 방법은 간단하다. 아래 스크린샷처럼 빨간 박스 쳐진 곳을 더블 클릭하면 campingListItemController.js 라는 이름으로 컨트롤러가 생성된다.<br>
![02](https://user-images.githubusercontent.com/69135840/209919887-56205d33-96f7-4403-961b-929ac682d22f.png)<br><br>
코드는 아래와 같다.
```javascript
// campingListItemController.js
({
    packItem : function(component, event, helper) {
        //파라미터 설명
        //component : 컴포넌트(cmp파일)에 있는 attributes, methods 등을 나타냄 
        //event : 컨트롤러(js파일)의 메서드를 실행하도록 만드는 것을 나타냄
        //helper : helper파일의 메서드를 불러오는데 쓰임 (여기선 쓰이지 않음)
        component.set("v.Packed__c", true); //Packed__c의 값을 true로 set
        let button = event.getSource(); 
        button.set("v.disabled", true); //버튼을 더 이상 클릭할 수 없도록 disabled 상태로 만듦
    }
})
```

결과 <br>
![03](https://user-images.githubusercontent.com/69135840/209921645-aa83bf6f-296b-4052-b2db-4584833ac465.png)