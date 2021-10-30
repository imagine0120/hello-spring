# hello-spring
* 강의 : [스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
* 공식문서 : https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features

## 스프링 웹 개발 기초
### 정적 컨텐츠
1. 스프링 기본 설정
  - 클래스패스 혹은 서블릿 컨텍스트의 루트 폴더의 /static (or /public or /resources or /META-INF/resources)
  - "/hello-static.html"이란 경로로 정적 콘텐츠 요청 시, 내장 톰캣이 컨트롤러 경로부터 탐색
  - 없을 경우 /resources/static 아래 정적 콘텐츠 리턴
  - static : 정적 콘텐츠들을 두는 폴더
  - templates : thymeleaf 파일들을 두는 폴더

### MVC와 템플릿 엔진
1. 파라미터 값 받기
  - @RequestParam : HTTP 요청 파라미터로 값을 전달받음. 해당 파라미터 없을 경우 에러나므로, required = false, 혹은 defaultValue 설정 필요
  - @RequestBody
  - ModelAttribute

2. 값 넘겨주기
