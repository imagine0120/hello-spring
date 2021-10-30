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
1. Model
  - addAttribute 통해서 값을 템플릿에 전달

2. viewResolver
  - String 값과 동명의 template 찾아서 리턴(템플릿 엔진 처리)

### API
1. @ResponseBody 문자 반환
  - template이 아닌, 문자 내용을 직접 반환(JSON)
  - viewResolver가 아닌, HttpMessageConverter가 동작
  - 기본 문자인 경우, StringHttpMessageConverter
  - 객체의 경우 MappingJackson2HttpMessageConverter가 객체를 JSON으로 반환

## 회원 리포지토리 테스트 케이스 작성
1. TDD
  - 테스트 클래스(케이스)를 먼저 작성하고, 이후 로직 생성 후 검증

## 회원 서비스 개발
1. Optional<Type>::ifPresent()
  - Consumer<? super Type> arg 를 받아서 리턴값이 없는 표현식(expression)을 실행시킴
  - 표현식은 arg를 매개변수로 사용할 수도, 사용하지 않을 수도 있음
  ```java
  Optional<Member> member = memberRepository.findById(id);
  member.ifPresent(m->{
    throw new IllegalStateException("이미 가입한 회원입니다.");
  });
  ```
