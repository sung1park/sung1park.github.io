---
layout: post
title: '[Spring] File Upload/Download'
date: 2021-10-22 18:00:00 +0900
category: spring
---

# 8. File Upload/Download

> 파일 업로드 및 다운로드



### 8-1. 파일 업로드

##### servlet-context.xml 설정

```xml
<!-- File Upload -->
<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <beans:property name="defaultEncoding" value="UTF-8"/>
    <beans:property name="maxUploadSize" value="52428800"/> 
    <beans:property name="maxInMemorySize" value="1048576"/> 
</beans:bean>
```

- maxUploadSize : 최대 업로드 가능한 파일의 바이트 크기
- maxInMemorySize : 디스크에 임시 파일을 생성하기 전에 메모리에 보관할 수 있는 최대 바이트 크기



##### jsp(html) 파일에 form 입력

```jsp
<form id="writeform" method="post" enctype="multipart/form-data">
    <div class="form-group" align="left">
        <lable for="upfile">파일: </lable>
        <input type="file" class="form-control" name="upfile" multiple>
    </div>
</form>
```

- form 태그의 enctype은 "multipart/form-data"로 설정
- input 태그의 type은 "file"로 설정하고, multiple 속성을 이용해 다중 파일 입력을 지원한다



##### GuestBookDto, FileInfoDto

```java
public class GuestBookDto {
	private int articleNo;
	private String userId;
	private String userName;
	private String subject;
	private String content;
	private String regTime;
	private List<FileInfoDto> fileInfos;
```

```java
public class FileInfoDto {
	private String saveFolder;
	private String originFile;
	private String saveFile;
```

- 각각의 게시물 마다 파일 정보를 가지도록 하기 위해서 기존의 GuestBookDto를 수정하고, FileInfoDto를 추가하였다
- FileInfoDto는 저장 폴더이름과 원래 파일이름, 실제 저장 파일이름을 가진다



##### GuestBookController

```java
@PostMapping("/register")
public String register(GuestBookDto guestBookDto, @RequestParam("upfile") MultipartFile[] files, Model model, HttpSession session, RedirectAttributes redirectAttributes) throws Exception {
    MemberDto memberDto = (MemberDto) session.getAttribute("userinfo");
    guestBookDto.setUserId(memberDto.getUserId());

    // FileUpload 관련 설정.
    logger.debug("MultipartFile.isEmpty : {}", files[0].isEmpty());
    if (!files[0].isEmpty()) {
        String realPath = servletContext.getRealPath("/upload");
        String today = new SimpleDateFormat("yyMMdd").format(new Date());
        String saveFolder = realPath + File.separator + today;
        File folder = new File(saveFolder);
        if (!folder.exists())
            folder.mkdirs();
        List<FileInfoDto> fileInfos = new ArrayList<FileInfoDto>();
        for (MultipartFile mfile : files) {
            FileInfoDto fileInfoDto = new FileInfoDto();
            String originalFileName = mfile.getOriginalFilename();
            if (!originalFileName.isEmpty()) {
                String saveFileName = UUID.randomUUID().toString() 
                         + originalFileName.substring(originalFileName.lastIndexOf('.'));
                fileInfoDto.setSaveFolder(today);
                fileInfoDto.setOriginFile(originalFileName);
                fileInfoDto.setSaveFile(saveFileName);
                // 파일이 실제 경로로 올라감
                mfile.transferTo(new File(folder, saveFileName));
            }
            fileInfos.add(fileInfoDto);
        }
        guestBookDto.setFileInfos(fileInfos);
    }
    guestBookService.registerArticle(guestBookDto);

    redirectAttributes.addFlashAttribute("msg", "글이 작성되었습니다.");
    return "redirect:/guestbook/list?pg=1&key=&word=";
}
```

- 파일의 업로드 경로는 `/{프로젝트 경로}/upload/{업로드 날짜}`로 지정한다
- 업로드 하려는 경로의 폴더가 없다면 `.mkdirs()`로 생성한다
- view에서 넘어온 파일의 개수 만큼 FileInfoDto를 만들어 파일 정보를 DTO에 입력하고 list에 담는다
- `mfile.transferTo(new File())`을 이용해 파일을 실제 경로에 올려놓는다



### 8-2. 파일 다운로드

##### servlet-context.xml 설정

```xml
<!-- File Download -->
<beans:bean id="fileDownLoadView" class="com.ssafy.guestbook.view.FileDownLoadView"/>

<!-- BeanNameViewResolver 설정 -->
<beans:bean id="fileViewResolver" class="org.springframework.web.servlet.view.BeanNameViewResolver">
    <beans:property name="order" value="0" />
</beans:bean> 
```



##### jsp 파일

```jsp
<script>
  // File Download
  $('.filedown').click(function() {
    alert("원본 : " + $(this).attr('ofile') + ", 실제 :  " + $(this).attr('sfile'));
    $(document).find('[name="sfolder"]').val($(this).attr('sfolder'));
    $(document).find('[name="ofile"]').val($(this).attr('ofile'));
    $(document).find('[name="sfile"]').val($(this).attr('sfile'));
    $('#downform').attr('action', '${root}/guestbook/download')
        .attr('method', 'get').submit();
  });
</script>

<c:if test="${!empty article.fileInfos}">
  <tr>
    <td colspan="2">
      <ul>
        <c:forEach var="file" items="${article.fileInfos}">
        <li>${file.originFile} <a href="#" class="filedown" sfolder="${file.saveFolder}" 
                        sfile="${file.saveFile}" ofile="${file.originFile}">[다운로드]</a>
        </c:forEach>
      </ul>
    </td>
  </tr>
</c:if>
```



##### GuestBookController

```java
@GetMapping("/download")
public ModelAndView downloadFile(@RequestParam Map<String, String> fileInfo) {
    return new ModelAndView("fileDownLoadView", "downloadFile", fileInfo);
}
```

- view에서 보낸 파일 정보를 받아 servlet-context.xml에서 설정해놓은 "fileDownloadView" 로 보낸다
- `ModelAndView(String viewName, String modelName, Object modelObject)`



##### FileDownloadView

```java
public class FileDownloadView extends AbstractView {

    public FileDownloadView() {
        setContentType("application/download; charset=UTF-8");
    }

    @Override
    protected void renderMergedOutputModel(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
        ServletContext ctx = getServletContext();
        String realPath = ctx.getRealPath("/upload");
		
        // 전송받은 모델(파일 정보)
        Map<String, Object> fileInfo = (Map<String, Object>) model.get("downloadFile"); 

        String saveFolder = (String) fileInfo.get("sfolder"); // 파일 경로
        String originalFile = (String) fileInfo.get("ofile"); // 원본 파일명
        String saveFile = (String) fileInfo.get("sfile"); // 실제 저장된 파일명
        File file = new File(realPath + File.separator + saveFolder, saveFile);

        response.setContentType(getContentType());
        response.setContentLength((int) file.length());

        String header = request.getHeader("User-Agent");
        boolean isIE = header.indexOf("MSIE") > -1 || header.indexOf("Trident") > -1;
        String fileName = null;
        // IE는 다르게 처리
        if (isIE) {
            fileName = URLEncoder.encode(originalFile, "UTF-8").replaceAll("\\+", "%20");
        } else {
            fileName = new String(originalFile.getBytes("UTF-8"), "ISO-8859-1");
        }
        response.setHeader("Content-Disposition", "attachment; filename=\"" + fileName + "\";");
        response.setHeader("Content-Transfer-Encoding", "binary");
        OutputStream out = response.getOutputStream();
        FileInputStream fis = null;
        try {
            fis = new FileInputStream(file);
            FileCopyUtils.copy(fis, out);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        out.flush();
    }
}
```

- 전달받은 파일 정보를 통해 실제 경로와 파일 이름을 입력하여 `FileCopyUtils.copy(fis, out)`으로 파일을 클라이언트에서 다운받게 한다

