---
title: "[Salesforce Trailhead] Aura Components Basic(5) - Connect to Salesforce with Server-Side Controllers"
categories:
  - Salesforce Trailhead
tags:
  - Salesforce
  - Trailhead
  - Aura Components Basic
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2023-01-02
---

### 들어가며

이 포스트는 세일즈포스 트레일헤드 中 [Aura Components Basics](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics) 모듈의 [Connect to Salesforce with Server-Side Controllers](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_server) 를 설명한다. 참고로 해당 모듈은 [Developer Intermediate](https://trailhead.salesforce.com/ko/content/learn/trails/force_com_dev_intermediate) 트레일의 하위 모듈이다.<br><br>
요구하는 과제는 아래와 같다. <br>
![00](https://user-images.githubusercontent.com/69135840/210201936-87f8adae-0400-4c22-856b-4ff1a64e1b77.png)<br><br>

[이전 포스트](https://kwonminho1992.github.io/salesforce%20trailhead/Input-Data-Using-Forms/)에서 새로운 camping item을 추가했을 때 화면에 추가된 item이 뜨도록 만들었다. 하지만 창을 닫고 새로 열면 추가됬던 item들은 온데간데 없을 것이다. 데이터베이스에 저장하지 않았기 때문이다. 그래서 창을 다시 열어도 이미 저장한 item이 출력되도록 하려면 item을 데이터베이스에 추가하는 과정이 필요하다.
<br><br>
이번 과제는 크게 두 과정으로 나뉜다.<br>
1. 이미 DB에 저장되있는 camping item들을 출력<br>
2. 새로운 camping item을 db에 추가<br><br>
![01](https://user-images.githubusercontent.com/69135840/210201937-fd86ce42-8538-4df7-960b-6ff7127d17bd.png)<br>
![02](https://user-images.githubusercontent.com/69135840/210201938-45b17586-e9fa-4474-a0ce-7ee4840eaf90.png)<br><br>

### 이미 DB에 저장되있는 camping item들을 출력하기
#### component & controller
이미 DB에 저장되있는 camping item들을 출력하기위해 controller에 doInit 메서드를 추가할 것이다. 그리고 이 메서드는 앱이 켜질 때(executer.app이 실행될 때) 실행될 것이다. 그러므로 일단 component에 aura:handler 태그를 통해 doInit 메서드를 실행하도록 만들 것이다.<br>
```html
<!-- campingList.cmp -->
<aura:component controller="CampingListController">
    <aura:handler name="init" action="{!c.doInit}" value="{!this}"/> <!-- doInit 메서드를 실행하기 위한 코드 -->
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
그리고 controller에 doInit 메서드를 추가한다.
```javascript
//campingListController.js (미완성)
({
    // Load camping items when run executer.app
    doInit: function(component, event, helper) {
        // Create the action from CampingListController.apxc
        let action = component.get("c.getItems");
        // Add callback behavior for when response from CampingListController.apxc is received
        action.setCallback(this, function(response) {
            let state = response.getState();
            if (state === "SUCCESS") {
                component.set("v.items", response.getReturnValue());
            }
            else {
                console.log("Failed with state: " + state);
            }
        });
        // Send action off to be executed
        $A.enqueueAction(action);
    },
    // insert new camping item into database 
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
#### apex
db에 이미 저장되있는 record(camping item)들을 가져오려면, SOQL의 select문을 통해 record들을 불러오는 과정이 있어야한다. 이를 위해 apex class를 생성하고 다음과 같이 코드를 작성하면 이미 DB에 저장되있는 camping item들을 출력하기는 끝난다.
```java
// CampingListController.apxc (미완성)
public class CampingListController {
    @auraEnabled
    public static List<Camping_Item__c> getItems() {
        List<Camping_Item__c> campingItems = [SELECT Name, Quantity__c, Price__c, Packed__c from Camping_Item__c];
        return campingItems;
    }
```

### 새로운 camping item을 db에 추가하기
#### controller & helper
새로운 camping item을 insert하기 위한 helper 메서드를 만들고, 해당 메서드는 controller의 clickCreateItem 메서드를 실행시킬 때 작동하도록 한다. 
```javascript
//campingListController.js (완성)
({
    // Load camping items when run executer.app
    doInit: function(component, event, helper) {
        // Create the action from CampingListController.apxc
        let action = component.get("c.getItems");
        // Add callback behavior for when response is received
        action.setCallback(this, function(response) {
            let state = response.getState();
            if (state === "SUCCESS") {
                component.set("v.items", response.getReturnValue());
            }
            else {
                console.log("Failed with state: " + state);
            }
        });
        // Send action off to be executed
        $A.enqueueAction(action);
    },
    // insert new camping item into database 
    clickCreateItem: function(component, event, helper) {       
        let validItem = component.find('campingform').reduce(function (validSoFar, inputCmp) {
            // 유효하지 않은 field 값 입력 시 띄워줄 에러메시지
            inputCmp.showHelpMessageIfInvalid();
            return validSoFar && inputCmp.get('v.validity').valid;
        }, true);
        // 유효하지 않은 field값이 없으면 items에 newItem 추가
        if(validItem){
            //createItem 메서드에서 item parameter 가져오기
            let item = event.getParam("campingItem");
            helper.createItem(component, item);
            
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
```javascript
//campingListHelper.js
({
    createItem : function(component, helper) {
        let action = component.get("c.saveItem");
        action.setParams({
            campingItem : component.get("v.newItem")
        });
        action.setCallback(this, function(response){
            let state = response.getState();
            if (state === "SUCCESS") {
                let items = component.get("v.items");
                items.push(response.getReturnValue());
                component.set("v.items", items);
                component.set("v.newItem",{ 'sobjectType': 'Camping_Item__c','Name': '','Quantity__c': 0,
                                           'Price__c': 0,'Packed__c': false });
            }
        });
        $A.enqueueAction(action); //apex action을 enqueue
    }
})
```
#### apex
camping item을 추가하는 일은 다르게 말하면 soql구문을 사용하여 db에 record를 insert하는 일이라 할 수 있다.<br> 이것을 apex 코드로 만들면 아래와 같다. 
```java
// CampingListController.apxc (완성)
public class CampingListController {
    @auraEnabled
    public static List<Camping_Item__c> getItems() {
        List<Camping_Item__c> campingItems = [SELECT Name, Quantity__c, Price__c, Packed__c from Camping_Item__c];
        return campingItems;
    }
    @auraEnabled
    public static Camping_Item__c saveItem(Camping_Item__c campingItem) {
        insert campingItem;
        return campingItem;        
    }
}
```
<br>
이제 Create Camping Item 버튼을 클릭하면 db에 camping item이 추가되고, 그렇게 추가된 camping item들을 doInit 메서드를 통해 화면에 출력할 수 있게 된다.
