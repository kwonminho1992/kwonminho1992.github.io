---
title: "@Value annotation"
categories:
  - Spring Boot
tags:
  - Spring Boot
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-09-25
---

# @Value annotation

Spring Boot로 개발을 할 때 편리한 점은 대부분의 설정을 application.properties 파일 (or yaml 파일) 하나에 몰아서 관리할 수 있다는 점이다. <br>
예를 들어 파일 다운로드 로직을 구현할 때, 파일을 다운로드하는 주소는 클래스 안에 직접 코딩할 수도 있다. 하지만 설정파일 하나에만 적어서 관리한다면 <br>
파일을 다운로드 받는 경로를 바꿔야 할 때, 설정파일에 써놓은 주소만 바꾸면 끝나기 때문에 유지 보수에 매우 편리할 것이다. 스프링부트에서는 이를 <br> 도와주는 annotation이 @Value이다. 사용법은 정말 간단하다.

```java
// application.properties
	#path to save file
    file.upload.directory=D:/files/

```

먼저 위의 application.properties에 파일 저장 경로를 뜻하는 이름을 짓는다. 여기서는 file.upload.directory 라고 지었는데, 어떤 이름을 쓰건 상관없다. <br>

```java
// Java code
@Component
public class ServiceFunctions {

  @Value("${file.upload.directory}")
  private String UPLOAD_PATH;

  public String findOriginalPath(int postNo, Dto dto) {
    String dtoName = dto.getName();
    String specificPath = "images" + File.separator + dtoName + "_images" + File.separator + dtoName
        + postNo + File.separator;
    String path = UPLOAD_PATH + specificPath;
    log.info(postNo + "번 " + dtoName + "의 이미지파일 저장 경로 : " + path);
    return path;
  }
}
```

@Value 어노테이션의 () 안에 application.properties에서 정한 file.upload.directory를 적는다. 그러면 @Value 어노테이션이 달린 상수 UPLOAD_PATH의 <br> 값이 D:/files/ 로 설정된다. 이렇게하면 file.upload.directory의 값이 바뀌면, 바뀐 값대로 UPLOAD_PATH의 값이 정해진다.
