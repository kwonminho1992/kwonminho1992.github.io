---
title: "[Spring Boot] 파일 업로드 & 다운로드 (1) - 파일업로드 로직 구현"
categories:
  - Spring Boot
tags:
  - Spring Boot
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-09-30
---

### 파일 저장 경로 설정

먼저 파일을 저장할 경로를 application.properties 파일에 적어준다. 여기에 적은 경로는 소스코드에서 @Value 어노테이션을 이용해 가져올 것이다. <br>

- @Value 어노테이션의 구체적인 사용법은 제 이전 포스트 - [@Value annotation](https://kwonminho1992.github.io/spring%20boot/value-annotation/) 참고<br>

```java
// application.properties
	#path to save file
  file.upload.directory=D:/files/
  #path to save file (temporary folder)
  file.upload.temp-directory=D:/temp/files/
```

### Service Code

파일 업로드 코드가 약간 복잡하므로 유지보수성을 높이기 위해 업로드 관련 코드는 다른 클래스로 분리하여 코드를 작성했다, Service class에 autowired하였다. <br>
또한 DTO 및 글 번호에 따라 저장경로를 다르게 설정하기 위해 saveFiles() method에 postNo, dto 라는 파라미터를 주었다.
dto 파라미터는 enum으로 만들었다.

```java
// enum code
@Getter
@AllArgsConstructor
public enum Dto {
  DIARY("diary"), NOTICE("notice");

  private final String name;
}

// file upload code
@Slf4j
@Component
public class ServiceFunctions {

  @Value("${file.upload.directory}")
  private String UPLOAD_PATH;
  @Value("${file.upload.temp-directory}")
  private String TEMP_UPLOAD_PATH;

  public void saveFiles(int postNo, Dto dto, List<MultipartFile> files)
      throws Exception {
    String path = findOriginalPath(postNo, dto);
    makeDirectories(path); // 디렉토리가 없을 경우, 디렉토리를 생성
    int fileNo = 0;
    for (MultipartFile file : files) {
      if (!file.isEmpty()) { // 파일이 null이 아닐 경우에 저장작업을 시작함
        String contentType = file.getContentType();
        fileNo++;
        File file = new File(path + fileNo + contentType);
        file.transferTo(file);
      }
      else {
        continue;
      }
    }
  }
  public void removeOriginalFiles(int postNo, Dto dto) {
    String path = findOriginalPath(postNo, dto);
    removeFolderAndFiles(path);
  }

  private void makeDirectories(String path) {
    File file = new File(path);
    if (!file.exists()) {
      file.mkdirs();
    }
  }

  private String findOriginalPath(int postNo, Dto dto) {
    // File.separator = window, linux, mac 등 서로 다른 운영체제에서 폴더경로를 인식하게 하는 역할
    String dtoName = dto.getName();
    String specificPath = "images" + File.separator + dtoName + "_images" + File.separator + dtoName
        + postNo + File.separator;
    String path = UPLOAD_PATH + specificPath;
    log.info(postNo + "번 " + dtoName + "의 이미지파일 저장 경로 : " + path);
    return path;
  }

    private void removeFolderAndFiles(String path) {
    File folder = new File(path);
    if (folder.exists()) {
      File[] allFiles = folder.listFiles();
      if (allFiles != null) {
        for (File file : allFiles) {
          file.delete();
        }
      }
      if (folder.isDirectory()) {
        folder.delete(); // 대상폴더 삭제
      }
    }
  }
}

// service (*interface 부분은 생략)
@Service(value = "DiaryService")
public class DiaryServiceImpl implements DiaryService {
  @Autowired
  private ServiceFunctions serviceFunctions;
  @Autowired
  private DiaryRepository diaryRepository;

  @Override
  public void writeDiary(Diary diary, List<MultipartFile> files) throws AddException {
    int diaryNo = 0;
    try {
      diaryRepository.insert(diary); // database에 diary(게시글) 행 1개 추가
      diaryNo = diaryRepository.selectLatestDiaryNo(); // 저장된 다이어리(게시글)의 diaryNo(postNo) 구하기

      serviceFunctions.saveFiles(diaryNo, Dto.DIARY, files); // 파일을 저장하는 메서드

    } catch (Exception e) {
      e.printStackTrace();
      serviceFunctions.removeOriginalFiles(diaryNo, Dto.DIARY);
      // 중간에 메서드 실행 실패 시, 다운로드 받아진 파일들을 삭제하는 삭제하는 메서드
      throw new AddException();
    }
  }
}

// file 저장 경로  :
// ex. D:/files/images/diary_images/diary1/1.jpg  (1번 다이어리의 1번 파일이라는 뜻)
// ex. D:/files/images/notice_images/notice3/1.pdf  (3번 공지사항의 1번 파일이라는 뜻)
```

### Controller Code

컨트롤러에서는 MultipartFile을 이용해서 프론트에서 파일을 받아온다. 하나의 파일만 받고 싶으면, 그냥 MultipartFile, 여러개를 받고 싶으면 List나 Array를 이용한다. 아래에서 설명하겠지만, 프론트에서 파일을 업로드 할때는 content type을 multipart/form-data로 해서 보낸다. 여기에 해당하는 컨트롤러 측의 어노테이션은 @RequestPart이므로, files 파라미터에 @RequestPart 어노테이션을 붙인다. 한편, 이렇게 받을 경우 Json파일을 인식하는데 문제가 있기 때문에 DTO는 dto type 대신 String type으로 받아 오고, ObjectMapper를 이용해 dto type으로 변환하였다.

```java
//controller
@RestController
@RequestMapping
public class DiaryController {
  @Autowired
  private DiaryService diaryService;

  @PostMapping(value = "diary/write")
  public ResponseEntity<?> writeDiary(@RequestPart List<MultipartFile> files, String diary,
      HttpSession session) throws AddException, NotLoginedException, EmptyContentException,
      JsonMappingException, JsonProcessingException {
    String clientId = (String) session.getAttribute("loginInfo");
    ObjectMapper mapper = new ObjectMapper();
    Diary d = null;
    d = mapper.readValue(diary, Diary.class);
    if (clientId == null) {
      throw new NotLoginedException(ErrorCode.NOT_LOGINED);
    } else if (d.getDiaryTitle().equals("") || d.getDiaryTitle() == null) {
      throw new EmptyContentException(ErrorCode.EMPTY_TITLE);
    } else if (d.getDiaryStartDate() == null || d.getDiaryEndDate() == null) {
      throw new EmptyContentException(ErrorCode.EMPTY_DATE);
    } else {
      Client client = new Client();
      client.setClientId(clientId);
      d.setClient(client);
      diaryService.writeDiary(d, files);
      return new ResponseEntity<>(HttpStatus.OK);
    }
  }
```

### Front Code

HTML에서는 일단 form 태그를 통해 객체를 컨트롤러로 보낸다. multipart/form-data를 사용하며, input 태그를 통해 업로드할 파일을 선택한다.
accept 속성을 이용하여 업로드 가능한 파일의 종류를 제한할 수 있다. (여기서는 이미지 파일로 제한해봄)

```html
<!-- head tag 생략 -->
<body>
  <form
    class="write-diary"
    method="post"
    action="/back/diary/write"
    enctype="multipart/form-data"
  >
    <div class="page-item">
      <br />
      <input
        type="text"
        class="form-control diary-title"
        placeholder="다이어리의 제목을 입력해주세요"
        required
      /><br />
      <input type="date" class="form-control diary-start-date" required />
      <small class="text-muted">여행 시작일</small>
      <input type="date" class="form-control diary-end-date" required />
      <small class="text-muted">여행 종료일</small><br /><br />
      <div class="form-group FileForm">
        <label>이미지 파일 업로드</label>
        <div class="custom-file">
          <input
            type="file"
            class="custom-file-input files"
            id="fileInput"
            name="files"
            accept="image/*"
            multiple
          />
          <label class="custom-file-label" data-breose="Browse"></label>
          <div class="images"></div>
        </div>
      </div>
    </div>
  </form>
</body>
```

AJAX 비동기처리로 프론트에서 컨트롤러로 파일과 게시글의 내용을 담은 json을 전송한다. (\*컨트롤러에서는 일단 String으로 받고 그다음에 dto type으로 변환한다.)

```javascript
$(() => {
  //-----ajax 요청-----
  $("form.write-diary").submit(() => {
    //----루트내용 가져오기----
    let diaryTitle = $("div.page-item>input.form-control.diary-title").val();
    let oldDiaryStartDate = $(
      "div.page-item>input.form-control.diary-start-date"
    ).val();
    let diaryStartDate = getFormatDate(oldDiaryStartDate);
    let oldDiaryEndDate = $(
      "div.page-item>input.form-control.diary-end-date"
    ).val();
    let diaryEndDate = getFormatDate(oldDiaryEndDate);
    //----- ajax 요청용 formData만들기
    let formData = new FormData();
    // diary 제출용 json객체 만들기
    let data = new Object();
    data.diaryTitle = diaryTitle;
    data.diaryStartDate = diaryStartDate;
    data.diaryEndDate = diaryEndDate;

    let diary = JSON.stringify(data);
    formData.append("diary", diary);
    //이미지 파일
    var fileInput = $("input.custom-file-input.files");
    for (var i = 0; i < fileInput.length; i++) {
      if (fileInput[i].files.length > 0) {
        for (var j = 0; j < fileInput[i].files.length; j++) {
          formData.append(
            "files",
            $("input.custom-file-input.files")[i].files[j]
          );
        }
      }
    }
    $.ajax({
      url: "back/diary/write",
      method: "post",
      contentType: false,
      processData: false,
      enctype: "multipart/form-data",
      data: formData,
      success: (jsonObj) => {
        alert(jsonObj.message);
        location.href = "diary_board.html";
      },
      error: (jqXHR) => {
        if (jqXHR.status == 500) {
          alert("서버 오류 : " + jqXHR.status);
        } else {
          alert(jqXHR.status + "오류 : " + jqXHR.responseJSON.message);
        }
      },
    });
    return false;
  });
});
```
