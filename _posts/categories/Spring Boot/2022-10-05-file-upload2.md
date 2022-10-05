---
title: "[Spring Boot] 파일 업로드 & 다운로드 (2) - 파일수정 로직 구현"
categories:
  - Spring Boot
tags:
  - Spring Boot
toc: true
toc_sticky: true
toc_label: "table of contents"
last_modified_at: 2022-10-05
---

### 로직 설명

파일 저장 로직은 각 게시글 당 폴더를 생성한 뒤에 해당 폴더에 파일들을 저장하는 방식으로 한다. 그런데 이미 저장된 파일을 수정할 때는 어떻게 할까? <br> 여러가지 방법이 있겠지만, 여기서는 이미 저장된 파일들을 모두 삭제하고, 게시글을 수정할 때 올라오는 파일로 모두 대체하는 방식을 사용하겠다. <br>
그런데 이 방식에도 문제가 있다. 만약 게시글을 수정하는 메서드가 중간에 실패한다면, 기존에 올라온 파일은 삭제되고 수정하면서 새로 올라오는 파일들을 저장하는 코드가 실행되기 전에 Exception이 발생할 것이다. 그렇다면 새 파일은 저장되지 못하고 기존 파일은 삭제되어버리는 문제가 발생할 것이다. <br>
그래서 게시글 수정 메서드를 실행하면 제일 먼저 이미 저장된 파일들을 임시폴더로 이동시킨 뒤, 메서드 실행에 성공하면 임시폴더는 삭제하고 새로운 파일들을 저장하게 할 것이다. 만약 중간에 실패한다면, 임시폴더로 옮겨졌던 파일들이 다시 원래 폴더로 복구될 것이다. <br>
![temp_file drawio](https://user-images.githubusercontent.com/69135840/194052071-554e36e7-8ba3-443f-a754-a32cbf9c8e2b.png) <br><br>
※ application.properties나 enum 등의 설정은 [파일 업로드 & 다운로드 (1) - 파일업로드 로직 구현](https://kwonminho1992.github.io/spring%20boot/file-upload/) 참고

### Service Code

```java
// file upload code
@Slf4j
@Component
public class ServiceFunctions {

  @Value("${file.upload.directory}")
  private String UPLOAD_PATH;
  @Value("${file.upload.temp-directory}")
  private String TEMP_UPLOAD_PATH;

  public void transferFilesToTempFolder(int postNo, Dto dto) {
    String tempPath = findTempPath(postNo, dto);
    makeDirectories(tempPath);
    String path = findOriginalPath(postNo, dto);

    copyFiles(path, tempPath);
  }

  public void transferFilesToOriginalFolder(int postNo, Dto dto) {
    String path = findOriginalPath(postNo, dto);
    makeDirectories(path);
    String tempPath = findTempPath(postNo, dto);

    copyFiles(tempPath, path);
  }

  private void copyFiles(String originalPath, String copiedPath) {
    File originalFolder = new File(originalPath);
    if (originalFolder.exists()) {
      File[] allOriginalFiles = originalFolder.listFiles();
      if (allOriginalFiles != null) {
        for (File originalFile : allOriginalFiles) {
          // 파일 복사
          String fileName = originalFile.getName();
          FileInputStream fis = null;
          FileOutputStream fos = null;
          try {
            fis = new FileInputStream(originalFile);
            File copiedFile = new File(copiedPath + fileName);
            fos = new FileOutputStream(copiedFile);
            int nRealByte = 0;
            while ((nRealByte = fis.read()) != -1) {
              fos.write(nRealByte);
            }
            fis.close();
            fos.close();
            originalFile.delete();
          } catch (Exception e) {
            e.printStackTrace();
            log.error("파일 복사 실패. 사유 : " + e.getLocalizedMessage());
          }
        }
      }
      if (originalFolder.isDirectory()) {
        originalFolder.delete(); // 대상폴더 삭제
      }
    }
  }

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

  private String findTempPath(int postNo, Dto dto) {
    String dtoName = dto.getName();
    String specificPath = "images" + File.separator + dtoName + "_images" + File.separator + dtoName
        + postNo + File.separator;
    String tempPath = TEMP_UPLOAD_PATH + specificPath;
    log.info(postNo + "번 " + dtoName + "의 이미지파일 임시저장 경로 : " + tempPath);
    return tempPath;
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
  @Transactional(rollbackFor = Exception.class)
  public void modifyDiary(Diary diary, List<MultipartFile> files) throws ModifyException {
    int diaryNo = diary.getDiaryNo();
    try {
      serviceFunctions.transferFilesToTempFolder(diaryNo, Dto.DIARY); // 원래 파일을 임시폴더로 이동
      diaryRepository.update(diary);
      serviceFunctions.saveFiles(diaryNo, Dto.DIARY, files, true); // 새롭게 업로드되는 파일들을 저장
      serviceFunctions.removeTempFiles(diaryNo, Dto.DIARY); // 임시폴더의 파일 삭제
    } catch (Exception e) {
      e.printStackTrace();
      serviceFunctions.transferFilesToOriginalFolder(diaryNo, Dto.DIARY); // 임시폴더의 파일들을 원래 폴더로 복구
      sqlSession.rollback();
      throw new ModifyException();
    }
  }
}

// file 저장 경로  :
// ex. D:/files/images/diary_images/diary1/1.jpg  (1번 다이어리의 1번 파일이라는 뜻)
// ex. D:/files/images/notice_images/notice3/1.pdf  (3번 공지사항의 1번 파일이라는 뜻)
```

### Controller Code

```java
//controller
@RestController
@RequestMapping
public class DiaryController {
  @Autowired
  private DiaryService diaryService;
  @PutMapping(value = "diary/{diaryNo}")
  public ResponseEntity<?> modifyDiary(@PathVariable int diaryNo,
      @RequestPart List<MultipartFile> files, String diary, HttpSession session)
      throws ModifyException, NotLoginedException, EmptyContentException, JsonMappingException,
      JsonProcessingException, NoPermissionException {
    String clientId = (String) session.getAttribute("loginInfo");
    ObjectMapper mapper = new ObjectMapper();
    Diary d = null;
    d = mapper.readValue(diary, Diary.class);
    if (clientId == null) {
      throw new NotLoginedException(ErrorCode.NOT_LOGINED);
    } else if (!clientId.equals(d.getClient().getClientId())) {
      throw new NoPermissionException(ErrorCode.NO_PERMISSION);
    } else if (d.getDiaryTitle().equals("") || d.getDiaryTitle() == null) {
      throw new EmptyContentException(ErrorCode.EMPTY_TITLE);
    } else if (d.getDiaryStartDate() == null || d.getDiaryEndDate() == null) {
      throw new EmptyContentException(ErrorCode.EMPTY_DATE);
    } else {
      Client client = new Client();
      client.setClientId(clientId);
      d.setClient(client);
      diaryService.modifyDiary(d, files);
      return new ResponseEntity<>(HttpStatus.OK);
    }
  }
```

프론트 코드는 파일 업로드하기에서의 코드와 거의 똑같기 때문에 생략한다.
