---
title: "[Salesforce Trailhead] Aura Components Basic(6) - Connect Components with Events"
categories:
  - Salesforce Trailhead
tags:
  - Salesforce
  - Trailhead
  - Aura Components Basic
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2023-01-03
---

### 들어가며

이 포스트는 세일즈포스 트레일헤드 中 [Aura Components Basics](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics) 모듈의 [Connect Components with Events](https://trailhead.salesforce.com/ko/content/learn/modules/lex_dev_lc_basics/lex_dev_lc_basics_events)를 설명한다. 참고로 해당 모듈은 [Developer Intermediate](https://trailhead.salesforce.com/ko/content/learn/trails/force_com_dev_intermediate) 트레일의 하위 모듈이다. <br><br>
요구하는 과제는 아래와 같다. <br>
![00](https://user-images.githubusercontent.com/69135840/210319483-6c3cda01-51ee-4203-9ca4-fbf637a86754.png)<br><br>
기존에는 campingList 컴포넌트이 camping item을 출력하는 역할, 새로운 camping item을 입력하는 역할을 모두 맡았는데, 이번 과제에서는 두 역할을 서로 다른 컴포넌트로 분리할 것이다. 역할에 따라 컴포넌트를 분리하는 것이 유지보수하기 쉽기 때문이다.<br>
그리고 camping item을 추가하는 Lightning event를 만들어서 두 컴포넌트에서 모두 사용할 수 있도록 할 것이다.<br>
큰 흐름을 보면 아래와 같다.<br>
![01](https://user-images.githubusercontent.com/69135840/210319488-f2ae858b-66dc-42e6-b4c2-0174ca04e2a7.png)<br>

### event
분리될 두 컴포넌트에서 사용될 이벤트를 만든다. 
```html
<!-- addItemEvent.evt -->
<aura:event type="COMPONENT" description="Event template">
    <aura:attribute name="item" type="Camping_Item__c"/> <!-- 해당 이벤트에서 쓰일 attribute의 이름은 item -->
</aura:event>
```

### component
먼저 campingList.cmp(camping item을 출력하는 역할)와 campingListForm.cmp(새로운 camping item을 입력하는 역할)를 분리할 것이다. 그리고 campingListForm.cmp는 **c:campingListForm**의 형태로 campingList.cmp 안에 넣을 것이다. <br><br>
campingList.cmp에서는 **aura:handler** 태그로 addItemEvent를 가져다 쓸 것이다.<br>
그리고 campingListForm.cmp에서는 **aura:registerEvent** 태그로 addItemEvent를 등록해둘 것이다.
#### campingList.cmp
```html
<!-- campingList.cmp -->
<aura:component controller="CampingListController">
    <aura:handler name="init" action="{!c.doInit}" value="{!this}"/>
    <aura:handler name="addItem" event="c:addItemEvent"
                  action="{!c.handleAddItem }"/> 
    
    <!-- ATTRIBUTES -->
    <aura:attribute name="items" type="Camping_Item__c[]"/>
    <aura:attribute name="newItem" type="Camping_Item__c" default="{'Name':'',
                                                                   'Quantity__c':0,
                                                                   'Price__c':0,
                                                                   'Packed__c':false,
                                                                   'sobjectType':'Camping_Item__c'}"/>
    <!-- / ATTRIBUTES -->
    <!-- CAMPING ITEM LIST FORM -->
    <c:campingListForm />
    
    <!-- /CAMPING ITEM LIST FORM -->  
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
#### campingListForm.cmp
```html
<!-- campingListForm.cmp -->
<aura:component controller="CampingListController">
    <!-- ATTRIBUTES -->
    <aura:attribute name="newItem" type="Camping_Item__c" default="{'Name':'',
                                                                   'Quantity__c':0,
                                                                   'Price__c':0,
                                                                   'Packed__c':false,
                                                                   'sobjectType':'Camping_Item__c'}"/>
    <!-- / ATTRIBUTES -->
    <aura:registerEvent name="addItem" type="c:addItemEvent"/>
    <lightning:layout >
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
</aura:component>
```

### controller & helper
#### campingListController.js
campingListController에서는 handleAddItem 메서드로 db에 new item을 추가한다.
```javascript
// campingListController.js
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
    handleAddItem: function(component, event, helper) {
        let addNewItem = event.getParam("item");
        let action = component.get("c.saveItem");
        action.setParams({
            campingItem : addNewItem 
        });
        action.setCallback(this, function(response){
            let state = response.getState();
            if (state === "SUCCESS") {
                let items = component.get("v.items");
                items.push(response.getReturnValue());
                component.set("v.items", items);
            }
        });
        $A.enqueueAction(action); //apex action을 enqueue
    }
})
```
#### campingListFormController.js
```javascript
// campingListFormController.js
({
    // insert new camping item into database 
    clickCreateItem: function(component, event, helper) {       
        let validItem = component.find('campingform').reduce(function (validSoFar, inputCmp) {
            // 유효하지 않은 field 값 입력 시 띄워줄 에러메시지
            inputCmp.showHelpMessageIfInvalid();
            return validSoFar && inputCmp.get('v.validity').valid;
        }, true);
        // 유효하지 않은 field값이 없으면 items에 newItem 추가
        if(validItem){
            let newItem = component.get("v.newItem");
            helper.createItem(component, newItem);        
        }
    }    
})
```
#### campingListFormHelper.js
```javascript
// campingListFormHelper.js
({
    createItem : function(component, newItem){
        let addEvent = component.getEvent("addItem");
        addEvent.setParams({
            item : newItem
        })
        addEvent.fire();
        component.set("v.newItem",{ 'sobjectType': 'Camping_Item__c','Name': '','Quantity__c': 0, 'Price__c': 0,'Packed__c': false });
    }
})
```