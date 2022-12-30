---
title: "[Salesforce Trailhead] Aura Components Basic(4) - Input Data Using Forms"
categories:
  - Salesforce Trailhead
tags:
  - Salesforce
  - Trailhead
  - Aura Components Basic
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-12-30
---

### 들어가며

이 포스트는 세일즈포스 트레일헤드 中 [Aura Components Basics](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics) 모듈의 [Input Data Using Forms](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_forms)를 설명한다.참고로 해당 모듈은 [Developer Intermediate](https://trailhead.salesforce.com/content/learn/trails/force_com_dev_intermediate) 트레일의 하위 모듈이다. <br><br>
요구하는 과제는 아래와 같다. <br>
![00](https://user-images.githubusercontent.com/69135840/210044641-fa31375b-f235-4180-877a-822d06ff402b.png)
### SLDS (Salesforce Lightning Design System)
SLDS란 세일즈포스에서 제공하는 UI 콜렉션이다. CSS styles, design guidelines, HTML templates이 포함되어 있어서 손쉽게 괜찮은 디자인을 만들 수 있다. 여기선 SLDS를 이용해 form을 만들 것이다. SLDS를 application에 적용시키려면 아래와 같이 코드를 작성한다.
```html
<!-- executer.app -->
<aura:application extends="force:slds">
    <!-- 여기엔 campingHeader 등의 component를 넣음 -->
    <c:campingHeader/>
    <c:campingList/>
</aura:application>
```
### Header 만들기
application을 실행할 때 SLDS를 적용하도록 했으니, 이제 component 코드를 짤 때 slds를 적용시킬 수 있다. layout 태그의 class를 slds-page-header로 하면 추가 코드 없이 header 스타일을 자동으로 적용할 수 있다. <br>
![02](https://user-images.githubusercontent.com/69135840/210046929-bc76d189-6201-445d-8416-679c3fc0cc5c.png)<br><br>

```html
<!-- campingHeader.cmp -->
<aura:component>
    <!-- PAGE HEADER -->
    <lightning:layout class="slds-page-header">
        <!-- ICON -->
        <lightning:layoutItem>
            <lightning:icon iconName="action:goal" alternativeText="Camping"/>
        </lightning:layoutItem>
        <!-- / ICON -->
        <!--  HEADER TEXT -->
        <lightning:layoutItem padding="horizontal-small">
            <h1 class="slds-text-heading_label">Camping List</h1>
            <h2 class="slds-text-heading_medium">My Camping List</h2>
        </lightning:layoutItem>
        <!-- / HEADER TEXT -->
    </lightning:layout>
    <!-- / PAGE HEADER -->
    
</aura:component>
```
실행하면 결과는 아래와 같다.<br>
![01](https://user-images.githubusercontent.com/69135840/210046923-a37e9f97-7a0c-4a36-a11c-e3099138981c.png)<br><br>
### form 만들기
#### component 부분
form은 아래와 같은 구조로 만들어진다.<br>
![03](https://user-images.githubusercontent.com/69135840/210051830-75bb97a6-7489-49be-8916-967e4e75e68d.png)<br><br>
그리고 form 바깥에 문제에서 요구하는대로 newItem, items attribute를 작성한다.
```html
<!-- campingList.cmp (*미완성) -->
<aura:component >
    <!-- ATTRIBUTES -->
    <aura:attribute name="items" type="Camping_Item__c[]"/>
    <aura:attribute name="newItem" type="Camping_Item__c" default="{'Name':'',
                                                                   'Quantity__c':0,
                                                                   'Price__c':0,
                                                                   'Packed__c':false,
                                                                   'sobjectType':'Camping_Item__c'}"/>
    <!-- / ATTRIBUTES -->
    <!-- CAMPING ITEM FORM -->  
    <lightning:layout>    
        <lightning:layoutItem padding="around-small" size="6">
            <!-- CREATE CAMPING ITEM -->
                <!-- BOXED AREA -->
                <fieldset class="slds-box slds-theme_default slds-container_small">
                    <legend id="newcampingform" class="slds-text-heading_small
                                                       slds-p-vertical_medium">
                        Add Camping Item
                    </legend>
                    <!-- CREATE NEW CAMPING ITEM FORM -->
                    <form class="slds-form_stacked">
                        <lightning:input aura:id="campingform" label="Name"
                                         name="Name"
                                         value="{!v.newItem.Name}"
                                         required="true"/>
                        <lightning:input aura:id="campingform" label="Quantity"
                                         type="number"
                                         name="Quantity"
                                         min="1"
                                         value="{!v.newItem.Quantity__c}"
                                         required="true"
                                         messageWhenRangeUnderflow="Enter minimum 1 Quantity"/>
                        <lightning:input aura:id="campingform" label="Price"
                                         type="number"
                                         name="Price"
                                         value="{!v.newItem.Price__c}"
                                         formatter="currency"/>
                        <lightning:input aura:id="campingform" label="Packed?"
                                         type="checkbox"
                                         name="Packed?"
                                         checked="{!v.newItem.Packed__c}"/>
                        <lightning:button label="Create Camping Item"
                                          class="slds-m-top_medium"
                                          variant="brand"
                                          onclick="{!c.clickCreateItem}"/>
                    </form>
                    <!-- / CREATE NEW CAMPING ITEM FORM -->
                </fieldset>
                <!-- / BOXED AREA -->
            <!-- / CREATE NEW CAMPING ITEM -->
        </lightning:layoutItem>
    </lightning:layout>
    <!-- / CAMPING ITEM FORM -->
</aura:component>
```
그럼 아래와 같이 화면이 만들어질 것이다. <br>
![04](https://user-images.githubusercontent.com/69135840/210051834-e1d1ea86-ce1a-41e1-af35-bfcd79be497c.png)<br><br>

#### controller 부분
이제 controller에서 items(camping item을 모아놓은 list)에 새 item을 추가하는 메서드를 만들 것이다. 코드는 아래와 같이 작성한다.
```javascript
//campingListController.js
({
    clickCreateItem: function(component, event, helper) {       
        let validItem = component.find('campingform').reduce(function (validSoFar, inputCmp) {
            // 유효하지 않은 field 값 입력 시 띄워줄 에러메시지
            inputCmp.showHelpMessageIfInvalid();
            return validSoFar && inputCmp.get('v.validity').valid;
        }, true);
        // 유효하지 않은 field값이 없으면 items에 newItem 추가
        if(validItem){
            let items = component.get("v.items");
            let newItem = component.get("v.newItem");
            let newItemByJson = JSON.parse(JSON.stringify(newItem));
            items.push(newItemByJson); // items에 newItem 추가
            console.log("Create new item: " + JSON.stringify(newItem));
            
            //items에 새롭게 추가된 부분 반영 & newItem은 default value로 돌려놓음
            component.set("v.items",items);
            component.set("v.newItem",{ 'sobjectType': 'Camping_Item__c','Name': '','Quantity__c': 0,
                                       'Price__c': 0,'Packed__c': false });
        }
    }
})
```
### camping item list 만들기
마지막으로 추가한 item들을 화면에 띄워볼 것이다. campingListItem.cmp에서 item을 띄우는 형식을 만들고, campingList.cmp에 aura:iteration(반복문) 태그를 통해 item들을 띄울 것이다.
```html
<!-- campingListItem.cmp -->
<aura:component>
    <aura:attribute name="item" type="Camping_Item__c"/>
                Name : "{!v.item.Name}" <br/>
                Quantity : "{!v.item.Quantity__c}" <br/>
                Price : "{!v.item.Price__c}" <br/>
                Packed : "{!v.item.Packed__c}" <br/>
                --------------------------<br/>
</aura:component>
```
<br>
```html
<!-- campingList.cmp (최종) -->
<aura:component >
    <!-- ATTRIBUTES -->
    <aura:attribute name="items" type="Camping_Item__c[]"/>
    <aura:attribute name="newItem" type="Camping_Item__c" default="{'Name':'',
                                                                   'Quantity__c':0,
                                                                   'Price__c':0,
                                                                   'Packed__c':false,
                                                                   'sobjectType':'Camping_Item__c'}"/>
    <!-- / ATTRIBUTES -->
    <!-- CAMPING ITEM FORM -->  
    <lightning:layout>    
        <lightning:layoutItem padding="around-small" size="6">
            <!-- CREATE CAMPING ITEM -->
                <!-- BOXED AREA -->
                <fieldset class="slds-box slds-theme_default slds-container_small">
                    <legend id="newcampingform" class="slds-text-heading_small
                                                       slds-p-vertical_medium">
                        Add Camping Item
                    </legend>
                    <!-- CREATE NEW CAMPING ITEM FORM -->
                    <form class="slds-form_stacked">
                        <lightning:input aura:id="campingform" label="Name"
                                         name="Name"
                                         value="{!v.newItem.Name}"
                                         required="true"/>
                        <lightning:input aura:id="campingform" label="Quantity"
                                         type="number"
                                         name="Quantity"
                                         min="1"
                                         value="{!v.newItem.Quantity__c}"
                                         required="true"
                                         messageWhenRangeUnderflow="Enter minimum 1 Quantity"/>
                        <lightning:input aura:id="campingform" label="Price"
                                         type="number"
                                         name="Price"
                                         value="{!v.newItem.Price__c}"
                                         formatter="currency"/>
                        <lightning:input aura:id="campingform" label="Packed?"
                                         type="checkbox"
                                         name="Packed?"
                                         checked="{!v.newItem.Packed__c}"/>
                        <lightning:button label="Create Camping Item"
                                          class="slds-m-top_medium"
                                          variant="brand"
                                          onclick="{!c.clickCreateItem}"/>
                    </form>
                    <!-- / CREATE NEW CAMPING ITEM FORM -->
                </fieldset>
                <!-- / BOXED AREA -->
            <!-- / CREATE NEW CAMPING ITEM -->
        </lightning:layoutItem>
    </lightning:layout>
    <!-- / CAMPING ITEM FORM -->
    <!-- CAMPING ITEM LIST -->
    <lightning:card title="Camping Items"> <!-- card형식의 디자인 사용 -->
        <p class="slds-p-horizontal_small">
            <aura:iteration items="{!v.items}" var="item">
                <c:campingListItem item="{!item}"/>            
            </aura:iteration>
        </p>
    </lightning:card>
    <!-- CAMPING ITEM LIST -->
</aura:component>
```
결과 <br>
![05](https://user-images.githubusercontent.com/69135840/210065191-8ca15706-267e-4cfc-8539-4db0381e7507.png)