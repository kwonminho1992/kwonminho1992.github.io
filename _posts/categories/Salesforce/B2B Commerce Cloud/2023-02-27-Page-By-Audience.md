---
title: "코드 없이 쉽게 사용자마다 다른 페이지 보여주기(Salesforce B2B Commerce Cloud, Experience Cloud)"
categories:
  - Salesforce B2B Commerce Cloud
tags:
  - Salesforce
  - B2B Commerce Cloud
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2023-02-28
---

* 이 포스트는 Commerce Cloud 및 Experience Cloud 사용을 위한 기본적인 세팅은 다 되어있다고 가정하고 진행한다. 만약 기본 세팅이 되어 있지 않다면
[Partner Learning Camp](https://partnerlearningcamp.salesforce.com/)의 Get to Know B2B Commerce for Administrators 커리큘럼의 B2B Commerce: Setting up Your Environment를 참고하여 세팅하면 된다. <br><br>

Experience Cloud의 Audience, B2B Commerce의 Buyer Group 기능은 홈페이지에 접속할 사람에 따라 다른 것들을 보여주는 기능으로, B2B Commerce site를 구축하는데 유용한 기능이다. 예를 들어 자신이 글로벌 규모의 패스트푸드 공급업체의 경영자이고, 전세계에 걸쳐 수십만개의 B2B 고객에게 식재료를 팔아야한다고 상상해보자. 그럼 일단 고객의 국적에 따라 페이지의 언어를 다르게 해야할 것이다. 베트남인 고객에게 한국어 페이지를 보여준다면 이해할 수 없을 것이다. 또한 국가마다 판매하는 제품도 달라진다. 힌두교를 믿는 인도에는 쇠고기 패티를 납품할 수 없다. 이슬람 국가에선 돼지고기를 빼야한다. 그리고 지역, 국가마다 특화된 재료와 메뉴가 있으니 그에 따라 추가되거나 빠지는 제품이 생긴다. 가격도 문제가 된다. 지역, 국가, 매장마다 햄버거 가격이 조금씩 차이가 있으니, 똑같은 튀김용 감자를 팔더라도 고객마다 가격이 달라져야한다. 여기서 끝이 아니다. 고객에 따라 판매할 식재료의 종류, 가격은 자주 바뀐다. 만약 이런 점들을 반영하기 위해 따로 홈페이지들을 만든다면 어떻게 될까? 제대로 관리하기 힘들 것이다. 하지만 Audience, Buyer Group 기능을 활용하면 설정 몇번 만으로 모두 같은 백엔드를 쓰지만 고객마다 다른 페이지를 보여줄 수 있다. 같은 홈페이지라도 인도 고객에겐 쇠고기 패티가 노출되지 않을 것이고, 이슬람권 고객에겐 돼지고기가 노출되지 않을 것이다.<br>
![19](https://user-images.githubusercontent.com/69135840/221777465-36e90ee3-b5d7-4ac8-90c5-97132be39854.png)

### Chapter 1. Website 생성하기
(이미 사용할 Experience 사이트가 있다면 이 챕터는 건너 뛰어도 된다.)<br><br>
Digital Experiences -> All Sites를 검색하면 아래와 같은 페이지가 나올 것이다. New를 클릭하여 새로운 Experience 사이트를 만든다.<br>
![02](https://user-images.githubusercontent.com/69135840/221752654-c489d6bb-c4e2-4a46-8c1f-f97b49b76d4b.png)<br>
탬플릿을 선택하라고 할텐데, 아래처럼 B2B Commerce를 선택한다.<br>
![03](https://user-images.githubusercontent.com/69135840/221752655-16bfdbee-a032-4f35-9fd1-c658e571034d.png)<br>
이름과 주소를 입력하고 생성하면, 새로운 사이트가 만들어질 것이다.<br>
![04](https://user-images.githubusercontent.com/69135840/221752657-4afe3fe7-835b-4d3a-a610-4f17b071efda.png)<br>

### Chapter 2. Audience 설정 
All sites에서 생성된 사이트의 Builder를 클릭한다. 그럼 빌드 화면이 나오는데, 여기서 톱니바퀴 버튼 -> page variations -> 사람모양(Manage Audience) 버튼을 클릭한다.<br>
![05](https://user-images.githubusercontent.com/69135840/221752635-5230e6bd-517b-46fd-bbfe-936c9897fdd4.png)<br>
그럼 새로운 Audience를 생성하라는 화면이 뜬다. 아래와 같이 System Administrator 프로필을 가진 경우와 가지지 않은 경우, 총 2개의 Audience를 생성한다.<br>
![06](https://user-images.githubusercontent.com/69135840/221752640-e5e39023-55e2-4ffb-b778-40ec779b9c19.png)<br>
이제 설정한 audience에 따라 다른 화면을 보여 줄 차례다. 같은 화면에서 New Page Variation 버튼을 클릭하여 페이지를 두개 더 (페이지 이름은 Home For Admin, Home For Not Admin으로 지었다) 만든다. 
![07](https://user-images.githubusercontent.com/69135840/221752641-386cb494-782e-4edc-af26-737db5d5b28d.png)<br>
그리고 생성한 페이지에 아래와 같이 Audience를 assign한다. 같은 Home 화면이지만, Audience에 따라 다른 페이지를 보여줄 수 있게 된다. 이제 아래와 같이 audience에 따라 페이지 구성을 다르게 해본다. admin audience에겐 Calendar가 보이도록, not admin audience에겐 Quick Order가 보이도록 만든다. <br>
Theme -> Manage Braning Sets로 들어가서 audience에 따라 다른 로고가 보이도록 만들 수도 있다.  <br>
![10](https://user-images.githubusercontent.com/69135840/221752650-7f6e857a-6d1b-40d2-9d71-a0c411a19412.png)<br>
이제 아래와 같이 Admin 계정으로 로그인할때와 그렇지 않을때 보이는 페이지가 달라진다. <br>
![09](https://user-images.githubusercontent.com/69135840/221752645-cf25ef90-03c0-44a3-82ac-d5ab9cafc520.png)<br>

### Chapter 3. Buyer 마다 다른 상품 노출
다시 패스트푸드 공급업체 이야기로 돌아가서, 인도 고객에게 보여줄 제품 목록은 튀김용 감자, 식용유, 채소패티, 마살라(인도식 향신료 믹스)이고, 이슬람권 고객에게 보여줄 제품 목록은 튀김용 감자, 식용유, 쇠고기패티, 햄버거 번(bun) 이라고 하자. (*예시이므로 언어는 바꾸지 않겠다)<br>
일단 Experience 페이지에서 상품이 노출되도록 설정 해야한다. 먼저, Commerce app -> Product로 들어간다. New 버튼을 눌러 아래와 같이 판매할 제품을 생성한다. <br>
![12](https://user-images.githubusercontent.com/69135840/221774848-cb2266d2-38a3-4821-a4fe-5d218605f8df.png)<br>
그 다음으로, Commerce app -> Store로 들어간다. Categories를 클릭하여 들어가고, Ingredient 카테고리를 생성한다. (*기존에 만들어둔 카테고리가 있어서 그건 비활성화했다.) <br> 
![11](https://user-images.githubusercontent.com/69135840/221774837-e2be1242-a2cc-488c-a0b9-da3f495bdb6a.png)<br>
생성한 카테고리 안으로 들어가 아까 만든 Product들을 assign해준다. (튀김용 감자, 식용유, 채소패티, 마살라, 쇠고기패티, 햄버거 번) <br>
![13](https://user-images.githubusercontent.com/69135840/221774853-90dc11be-9d10-429e-b4aa-d3aa57ca1d0b.png)<br>
다시 Store에 들어가서 아래처럼 Build Your Search Index -> Rebuild Index 를 클릭하여 Experience 페이지에 추가한 Product들을 등록한다.<br>
![17](https://user-images.githubusercontent.com/69135840/221774861-cc789adb-fcdf-4b86-aba7-5ac3a6959f66.png)<br>
이제 고객에 따라 보여줄 상품을 다르게 할 차례다. Commerce app -> Entitlement Policies로 들어가 아래와 같이 인도 고객용, 이슬람 고객용 policy를 만들어준다. <br>
![14](https://user-images.githubusercontent.com/69135840/221774856-9cbc5aec-c137-488b-a952-1998cbf19e80.png)<br>
그리고 나서 Commerce app -> Buyer Groups에서 마찬가지로 인도, 이슬람 두개의 바이어 그룹을 만든다. <br>
![15](https://user-images.githubusercontent.com/69135840/221774858-d70f7079-d23b-4727-b188-6774df90a0c1.png)<br>
참고로 Buyer Group Member는 Buyer Account 중에서 선택할 수 있다. 그러므로 생성한 Account들 중 일부를 아래처럼 Enable As Buyer 하고 Buyer Groups에 추가한다<br>
![16](https://user-images.githubusercontent.com/69135840/221774860-d93b7a2f-3b0a-4079-a116-52ecc7751b1f.png)<br>
이제 인도 고객과 이슬람 고객의 아이디로 각각 로그인하면 서로 다른 제품 목록이 뜨는 것을 확인할 수 있을 것이다.<br>
![18](https://user-images.githubusercontent.com/69135840/221774864-2d965af7-a160-4954-b772-3c3fd8a2f67d.png)<br>