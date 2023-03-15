---
title: "Checkout flow debug하기(Salesforce B2B Commerce Cloud)"
categories:
  - Salesforce B2B Commerce Cloud
tags:
  - Salesforce
  - B2B Commerce Cloud
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2023-03-15
---

* 이 포스트는 Commerce Cloud 사용을 위한 기본적인 세팅은 다 되어있다고 가정하고 진행한다. 만약 기본 세팅이 되어 있지 않다면
[Partner Learning Camp](https://partnerlearningcamp.salesforce.com/)의 Get to Know B2B Commerce for Administrators 커리큘럼의 B2B Commerce: Setting up Your Environment를 참고하여 세팅하면 된다. <br><br>


### 들어가며
온라인 쇼핑몰에서 물건을 주문할 때를 생각해보자. 주문할 물건을 선택한 후 주문 버튼을 누른다고 바로 주문이 되지는 않는다. 주문화면에 들어오면 배송지 주소, 결제수단 등을 다 입력한 다음에야 주문이 성사된다. B2B 거래에서도 마찬가지로 주문이 성사되기까지 배송지 주소입력, 결제수단 입력 등 여러 과정을 거쳐야한다. <br><br>
![01](https://user-images.githubusercontent.com/69135840/225182977-1f5d8b24-c6f9-4517-9bb5-227184c273c5.png)<br>
B2B Commerce Cloud에서 주문을 시작하면, 위의 스크린샷처럼 배송지 주소 입력 등의 과정을 거쳐야 주문이 성사된다.<br><br>
Saleforce B2B Commerce Cloud에서 이런 과정을 구현해주는 것이 checkout flow이다. <br> checkout flow를 사용하면 복잡한 코드 없이 비교적 쉽게 주문 프로세스를 구현할 수 있다는 장점이 있다. 하지만 복잡한 코드가 있건 없건 간에 디버깅이 있어야 한다. 그래서 이 글에서는 checkout flow를 개발할 때 어떻게 해야 효과적으로 디버깅 할 수 있는지 소개하려고 한다. <br> 

### Debug 버튼 활용
만들어 놓은 Checkout Flow Template이 있다면 거기에 들어가고, 아니면 기본으로 제공해주는 Checkout Flow Template에 들어간다.<br>
![02](https://user-images.githubusercontent.com/69135840/225184522-0a81538d-45d3-4e39-b06f-6134632d0bd2.png)<br><br>
그런 다음에 아래 스크린샷처럼 Debug 버튼을 클릭하면 해당 Checkout flow의 디버깅을 시작할 수 있다.<br><br>
![03](https://user-images.githubusercontent.com/69135840/225209691-2ca0c75d-cac5-4048-969e-d92e0d548f2d.png)<br>
그러면 아래와 같은 화면이 뜰텐데, Run flow as another user를 선택하고, User는 주문을 하려는 계정(카드에 물건을 담고 주문버튼을 누른 계정)을 선택한다. cartId 값도 SOQL query를 이용하여 찾아내서 입력한다. 그리고 debug를 선택하고 Run을 눌러 디버깅을 시작한다.<br>
![04](https://user-images.githubusercontent.com/69135840/225209692-aaaa1e35-9659-47a6-be36-301c66c728b9.png)<br><br>
그럼 아래 스크린샷처럼 하나씩 element를 넘길 때 마다 오른쪽의 Debug detail에서 실행내용을 볼 수 있다.<br>
![05](https://user-images.githubusercontent.com/69135840/225211560-3d1c3f24-3b90-4ef3-a4a6-d218e5fbe58f.png)<br><br>
SHIP TO에선 물건을 구매하려는 Account에 등록된 Contact Point Address 들 중 하나를 선택할 수 있다. <br>
그런데 만약 SHIP TO에서 'NA, Factoryville, PA 18419 United States'에 해당하는 Contact Point Address record를 제대로 선택했는지 확인하려면 어떻게 해야할까? Debug Details에선 해당 Contact Point Address record의 address field를 제대로 선택했는지, 혹시 버그가 있어서 다른 주소를 선택한건지 정확히 확인할 수는 없다.<br>
![06](https://user-images.githubusercontent.com/69135840/225209698-be2db7df-8e7c-426e-acfb-bd5db5988e36.png)<br><br>
그래서 Subflow-Shipping Address flow의 Get Reords element 다음에 Debug Screen이라는 이름으로 Screen element를 하나 붙여줬다. 이것을 붙이면, 화면으로 원하는 Contact Point Address record의 field값들을 제대로 불러왔는지 확인할 수 있다. 만약 여러개의 field 값을 한번에 확인해야한다면 formula를 활용하여 한번에 확인할 수 있다.<br>

![07](https://user-images.githubusercontent.com/69135840/225209701-4fbb6502-012f-4859-b8c3-1c710e5375a4.png)<br>
![08](https://user-images.githubusercontent.com/69135840/225209703-37931407-3d2b-4d30-b291-231ff3d05022.png)<br>
```java
// 참고 : formula 내용
" Contact Point Address Record Information in Subflow - Shipping Address (Get Contact Point Address Record) /// ID : " + 
IF(ISBLANK({!ContactPointAddressRecord.Id}), "null", {!ContactPointAddressRecord.Id}) +
" / Name : "+ IF(ISBLANK({!ContactPointAddressRecord.Name}), "null", {!ContactPointAddressRecord.Name}) +
" / Country : "+ IF(ISBLANK({!ContactPointAddressRecord.Country}), "null", {!ContactPointAddressRecord.Country}) +
" / State : "+ IF(ISBLANK({!ContactPointAddressRecord.State}), "null", {!ContactPointAddressRecord.State}) +
" / City : "+ IF(ISBLANK({!ContactPointAddressRecord.City}), "null", {!ContactPointAddressRecord.City}) +
" / Street : "+ IF(ISBLANK({!ContactPointAddressRecord.Street}), "null", {!ContactPointAddressRecord.Street}) +
" / PostalCode : "+ IF(ISBLANK({!ContactPointAddressRecord.PostalCode}), "null", {!ContactPointAddressRecord.PostalCode})
``` 

### Debug log 활용
아래의 스크린샷 처럼 Checkout flow를 디버그할 때마다 Debug log를 남길 수도 있다. 
![09](https://user-images.githubusercontent.com/69135840/225209707-602dfb4b-5a65-430c-909b-bea69b337637.png)<br><br>
Setup의 Quick find 박스에서 Debug Logs를 선택해 들어간다. <br>
User Trace Flags의 New 버튼을 눌러 새로운 Trace flag를 만들 수 있다.<br> 아래의 스크린샷은 2023년 3월 15일 오후 1:21~51 사이에 debug를 할 경우, log가 남도록 설정한 것이다. 자신에게 맞게 설정하고 Save를 누른다. (*New Debug Level을 클릭하면 어느 레벨에서 로그를 남길지 정할 수 있음. Info level 이상? Debug level 이상?) Trace flag가 없으면 로그를 남길 수 없으니 반드시 만들어 둔다.
![10](https://user-images.githubusercontent.com/69135840/225209711-034141c5-6f0b-4d4a-a6b8-bc5ff72626e3.png)<br><br>
이제 Contact Point Address record의 address field를 로그에 남겨보자. 일단 debug log를 남기기 위한 apex class를 만들어야한다. <br>
Setup의 Quick find 박스에서 apex classes를 선택해 들어간다.<br>
그다음 아래와 같이 apex class를 하나 생성한다. <br>
![12](https://user-images.githubusercontent.com/69135840/225209715-2bdd57c6-7a2d-4817-bdbe-4d26c9074868.png)<br>
```java
//checkout flow들의 debug를 위해 쓰이는 클래스. 필요에 맞게 수정해서 사용가능
public with sharing class FlowDebugLogger {
    @InvocableMethod(label='Log Debug Message' description='Outputs messages to the debug log')
    public static void trace(List<String> debugMessages) {
        for (String debugMessage : debugMessages) {
            system.debug(debugMessage);
        }
    }
}
```
다시 flow로 돌아가서 action element를 하나 만든다. Action에 아까 만든 apex class(label = Log Debug Message)를 선택한다. debugMessages에 아까 만든 formula를 입력하면, debug level로 아까 만든 formula의 내용이 log로 저장된다.<br>
![11](https://user-images.githubusercontent.com/69135840/225209713-a30ac468-321f-4f3d-9793-876e5944e0c3.png)<br>
![13](https://user-images.githubusercontent.com/69135840/225209717-1641ad1f-aee4-4a06-b7c4-2007223f6449.png)<br><br>
