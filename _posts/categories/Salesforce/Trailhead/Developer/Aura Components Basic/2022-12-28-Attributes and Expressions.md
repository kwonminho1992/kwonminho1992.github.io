---
title: "[Salesforce Trailhead] Aura Components Basic(2) - Attributes and Expressions"
categories:
  - Salesforce Trailhead
tags:
  - Salesforce
  - Trailhead
  - Aura Components Basic
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-12-28
---

### 들어가며

이 포스트는 세일즈포스 트레일헤드 中 [Aura Components Basics](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics) 모듈의 [Attributes and Expressions](https://trailhead.salesforce.com/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_attributes_expressions?trail_id=force_com_dev_intermediate)를 설명한다.참고로 해당 모듈은 [Developer Intermediate](https://trailhead.salesforce.com/content/learn/trails/force_com_dev_intermediate) 트레일의 하위 모듈이다. <br><br>
### attribute 태그
먼저 요구하는 과제는 아래와 같다. <br>
![00](https://user-images.githubusercontent.com/69135840/209771788-13a36d9f-21c0-41c3-a8b5-7aa72166486a.png)<br><br>
여기서 attribute는 Account, Lead, Opportunity, Case 같은 object를 다룰때 쓰는 태그이다. 여기선 [Before You Start](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_prereqs)에서 미리 만든 Camping Item object와 그 하위 field들을 다룰 것이다. 이것을 그림으로 표현하면 아래와 같다. <br>
![01](https://user-images.githubusercontent.com/69135840/209771737-54c4ff97-d384-4ce7-a300-5546b5e91796.png)<br>
![02](https://user-images.githubusercontent.com/69135840/209771740-8e13079a-9273-4b64-9149-971ea343c4f2.png)<br><br>
과제에선 attribute의 이름을 item으로 하라 했으므로, 그림에서도 item이라고 표기하였다. 굳이 attribute의 이름을 Camping Item이라 할 필요는 없고 어떤 것이든 괜찮다. 이름에 따라 attribute의 구조가 결정되는 것이 아니라 type에 따라 결정되기 때문이다. 따라서 type을 Camping_Item__c (침고. type에는 api name을 작성해야함)로 정했다. <br>
그리고 하위 field들은 {!v.ATTRIBUTE NAME.FIELD NAME}의 형식으로 나타낸다. 이제 코드로 작성해보자
```html
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
                         label="Pcked?"
                         name="Packed"
                         checked="{!v.item.Packed__c}" />
        <!-- item attribute의 Packed__c field 출력 (토글 타입으로 표시됨) -->
    </p>
</aura:component>
```
화면으로 출력하면 아래와 같이 나온다. 어떤 필드를 출력할지는 작성했지만, 출력할 값이 없으므로 빈칸으로 보일 것이다.<br> 
![03](https://user-images.githubusercontent.com/69135840/209776384-d8ee926b-2d8b-43a1-a590-f1dc92f79382.png)<br><br>

#### 추가정보
attribute는 Lead, Opportunity, Account같은 salesforce object 뿐만 아니라 Apex에서 선언한 object, List 같은 자료형 등도 사용할 수 있다.<br>
![04](https://user-images.githubusercontent.com/69135840/209778348-71376474-6586-433c-934e-a2d919198256.png)<br>
```html
<!-- 위의 스크린샷을 나타낸 코드 -->
<aura:component>
    <aura:attribute name="messages" type="List"
        default="['You look nice today.',
            'Great weather we\'re having.',
            'How are you?']"/>
    <h2>List Items</h2>
    <p>message1="{!v.messages[0]}"</p>
    <p>message2="{!v.messages[1]}"</p>
    <p>message3="{!v.messages[2]}"</p>
</aura:component>
```
![05](https://user-images.githubusercontent.com/69135840/209778352-d3d3378f-c1fd-4015-b52f-464e51d4af01.png)<br><br>

반복문이나 조건문을 사용해서 출력할 수도 있다.
```html
<!-- 반복문 사용 -->
<aura:component>
    <aura:attribute name="messages" type="List"
        default="['You look nice today.',
            'Great weather we\'re having.',
            'How are you?']"/>
    <h2>List Items By Iteration</h2>
    <aura:iteration items="{!v.messages}" var="msg">
        <p>message="{!msg}"</p>
    </aura:iteration>
</aura:component>
```
```html
<!-- 조건문 사용 -->
<aura:component>
    <aura:attribute name="messages" type="List"
                    default="['You look nice today.',
                             'Great weather we\'re having.',
                             'How are you?']"/>
    <h2>List Items By if~else</h2>
    <aura:if isTrue="{!$Browser.isIPhone}">
        <p>message="{!v.messages[0]}"</p>
        <aura:set attribute="else">
            <p>message="{!v.messages[1]}"</p>
        </aura:set>
    </aura:if>
</aura:component>
<!-- {!$Browser.isIPhone} == true 인 경우, message="You look nice today." -->
<!-- {!$Browser.isIPhone} == false 인 경우, message="Great weather we're having." -->
```