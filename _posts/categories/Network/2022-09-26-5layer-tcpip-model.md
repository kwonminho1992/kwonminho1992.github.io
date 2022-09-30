---
title: "5 layer TCP/IP model"
categories:
  - Network
tags:
  - TCP/IP
  - Network
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-09-26
---

### 기본설명

네트워크 모델에는 크게 OSI 7 layer model, TCP/IP model 두가지가 있다. <br>
현재는 TCP/IP model이 주류이지만, 이것도 시간이 흐르자 업데이트된 버전이 나왔다. 기존의 TCP/IP model은 4개의 layer로 구성되었지만, 업데이트된 모델은 5개의 layer로 구성된다.<br>
![01](https://user-images.githubusercontent.com/69135840/192264411-010945ef-832b-4d24-89b2-d117a29dce4c.png)
이 포스트에서는 5 layer TCP/IP model을 설명할 것이다. <br>

### layer 1 : physical layer

컴퓨터 간의 통신은 0,1로 된 이진수 정보를 주고 받으면서 이루어진다. 물론 이것을 그냥 할 수는 없고, 전기신호로 바꾸어 전선으로 전달한다. <br>
대략 컴퓨터1 -> 이진수 정보 -> 인코딩 -> 전기 신호(아날로그 신호) -> 전선 -> 전기신호(아날로그 신호) -> 디코딩 -> 이진수 정보 -> 컴퓨터2 의 흐름이다. <br>
이렇게 이진수 정보를 전기신호로 바꾸어 전달하는 과정을 하는 layer가 physical layer이다. <br>
![02](https://user-images.githubusercontent.com/69135840/192216901-1947a1e7-7bd0-4fe6-9579-3a8458906468.png)

### layer 2 : datalink layer

위에서 말한 physical layer을 이용하면 컴퓨터간의 통신이 가능해진다. 하지만 2대가 아닌 여러대의 컴퓨터가 서로 통신을 주고 받아야하는 상황이라면, 상황이 조금 복잡해진다. <br>
여러대의 컴퓨터에서 동시에 데이터가 오는 상황에서, 어느 컴퓨터에서 온 데이터인지 구분하지 않는다면 데이터들이 뒤섞여 무엇이 무엇인지 알 수 없게 되버리기 때문이다. <br>
그래서 데이터의 양 끝에 다른 데이터와 구분해주는 구분자를 붙이는 작업을 해야하는데, 이 작업을 처리하는 곳이 datalink layer이다. <br>
![03](https://user-images.githubusercontent.com/69135840/192220470-5fce43be-019f-4e58-b79e-63c83c4bc656.png)

### layer 3 : network layer

network layer를 설명하기 전에 간단하게 인터넷의 원리에 대해 먼저 살펴보자. 만약 2~3대 정도의 컴퓨터끼리만 통신해야한다면, 해당 컴퓨터들 사이에만 전선을 연결하여 통신해도 딱히 상관 없을것이다.
하지만 이게 수십, 수백대가 된다면 감당이 안될 것이다. 그래서 몇대의 컴퓨터를 묶어 그 컴퓨터들이 외부와 통신할 수 있도록 매개체(라우터)를 둔다. 또한 이런 라우터들을 묶어서 상위의
라우터를 둘 수도 있다. 이런식으로 계속 상위의 라우터를 두다보면 전세계의 모든 컴퓨터와 연결할 수 있게 되는데, 이것이 인터넷이다.<br><br>
다시 본론으로 돌아가서, 이렇게 수많은 컴퓨터와 통신해야한다면 통신하려는 특정 컴퓨터의 주소값을 알고 있어야할 것이다. 그 주소값을 IP(Internet protocol) address라고 하며, network layer에서는 전달하려는 데이터에
상대 컴퓨터의 IP주소를 붙여주는 역할을 한다. <br>
![04](https://user-images.githubusercontent.com/69135840/192443385-54cdaedb-88ea-4713-b814-97cb408dcedc.png) <br>
참고 : 실제로 웹 서핑을 할 때, IP주소를 직접 입력하여 웹사이트에 접속하는 경우는 거의 없다. IP주소를 이루는 숫자는 아무런 의미가 없기 때문에 인간의 머리로는 기억하기 힘들다.
그래서 IP주소를 문자형태 (ex. www.google.com) 로 바꾸어서 사용하는데, 이것을 DNS(domain name system)이라고 한다. <br>

### layer 4 : transport layer

network layer를 통해 데이터를 받을 특정 컴퓨터를 정했다. 하지만, 그것으로 끝이 아니라 어느 프로세스에 보내야할지도 정해야한다. 컴퓨터를 하면서 하나의 프로그램만 실행하는 경우는 거의 없기 때문이다.
여러 웹사이트에 접속한 상태에서 데이터가 들어온다고 생각해보자. 그럼 수신받은 데이터를 어느 웹사이트에서 띄워야할까? IP주소만으로는 그것을 판단하기 어렵다. 그래서 포트번호를 두어 컴퓨터에서 실행 중인
각 프로세스를 구별한다. 이 작업을 하는 layer가 바로 transport layer다. <br>
![05](https://user-images.githubusercontent.com/69135840/192443390-8096d288-01cf-41b4-8c29-9623534b8592.png)

### layer 5 : application layer

application layer는 아마 웹개발자들에게 가장 익숙한 layer일 것이다. HTTP, FTP 등이 여기에 속한다. (HTTP 기준으로) 주고받는 데이터가 유효한 데이터(http status code)인지 아닌지,
아니면 조회/추가/수정/삭제용(RESTful)인지 등을 정할 수 있다. <br>
![06](https://user-images.githubusercontent.com/69135840/192443397-19b1f97d-ef36-4386-9148-62900ef6887d.png)

### 참고자료

[Five layered TCP/IP model](https://www.omnisecu.com/tcpip/five-layered-tcpip-model.php)<br>
[[10분 테코톡] 🔮 히히의 OSI 7 Layer](https://youtu.be/1pfTxp25MA8)<br>
