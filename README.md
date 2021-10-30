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


## 회원 관리 예제 - 백엔드 개발
### 회원 리포지토리 테스트 케이스 작성
1. TDD
  - 테스트 클래스(케이스)를 먼저 작성하고, 이후 로직 생성 후 검증

### 회원 서비스 개발
1. Optional<Type>::ifPresent()
  - Consumer<? super Type> arg 를 받아서 리턴값이 없는 표현식(expression)을 실행시킴
  - 표현식은 arg를 매개변수로 사용할 수도, 사용하지 않을 수도 있음
  ```java
  Optional<Member> member = memberRepository.findById(id);
  member.ifPresent(m->{
    throw new IllegalStateException("이미 가입한 회원입니다.");
  });
  ```
2. Refactor this shortcut
  - Ctrl + Shift + Alt + t (Windows)

### 회원 서비스 테스트
1. 테스트 클래스명은 한글로 해도 무방
2. given, when, then 사용하기
3. 예외 테스트 케이스 작성도 중요하다
4. 변수 추출하기 : Ctrl + Alt + V (Windows)
5. 두 개의 MemoryMemberRepository 인스턴스?
  - clearStore 메서드 추가 위해 테스트 클래스에도 MemoryMemberRepository 인스턴스 선언
  - 그런데 MemberService에서도 MemoryMemberRepository 인스턴스를 선언해주고 있음
  - clearStore의 대상인 store가 클래스 레벨의 static 변수이기 때문에 지금은 문제가 없음
  - 하지만 굳이 두 개의 인스턴스를 선언해 사용하는 것은 바람직하지 않음
  -> DI(Dependency Injection) 으로 해결! 생성자 주입 방식으로 MemberService에 MemoryMemberRepository를 주입시켜줌


## 스프링 빈과 의존관계
### 컴포넌트 스캔과 자동 의존관계 설정
1. Spring Container
  - MemberService 여러 컨트롤러가 공용으로 사용할 수 있는 서비스는 굳이 new 생성자로 여러 인스턴스를 만들 필요 없음
  - 스프링이 관리하는 컨테이너에 단 하나의 인스턴스만 만들어두고 가져다 쓰면 됨
  - 스프링이 POJO(Plain Old Java Object)를 스프링 컨테이너에서 관리해야 하도록 알려주려면, @Controller, @Service, @Repository, @Entity 와 같이 어노테이션 사용
  - 이와 같이 직접 사용할 인스턴스를 선언하는 것이 아니라, 스프링에 생성 및 관리를 맡기는 것이 바로 Dependency Injection

```java
MemberService memberService;

@Autowired
public MemberController(MemberService memberService){
  this.memberService = memberService;
}
```

2. Lombok & @RequiredArgsConstructor
  - 필드 주입 방식 대신 생성자 주입 방식이 권장됨
  - 생성자에 매개변수로 필요한 서비스나 리포지토리를 넣어주고, 생성자에 @Autowired 선언
  - private final로 필드에 선언 후, @RequiredArgsConstructor를 이용하면 간결해짐

```java
@RequiredArgsConstructor
@Controller
public class MemberController {

  private final MemberService memberService

}
```

3.  스프링 빈 등록 방법
  - 직접 스프링 빈 등록
  - 컴포넌트 스캔과 자동 의존관계 설정(DI) : ServletContext의 root 폴더 및 그 하위 폴더에서 어노테이션붙은 클래스를 탐색

### 자바 코드로 직접 스프링 빈 등록하기
1. root 폴더에 SpringConfig 생성
  - @Configuration 설정
  - 서비스나 리포지토리 생성자 선언 및 @Bean 설정
2. 장점
  -  상황에 따라 구현 클래스를 변경해야 할 때 유용함 (MemoryMemberRepository)

## 회원 관리 예제 - 웹 MVC 개발
### 회원 웹 기능 - 홈 화면 추가
1. HomeController
  - 경로를 "/"로 설정 시, 컨트롤러에 매핑된 경로가 우선시되어 resources/static/index.html 은 무시됨

### 회원 웹 기능 - 등록
1. POST 메서드로 등록요청 받기
  - form 태그 내 action 속성에 매핑된 경로로 input 값이 지정된 name으로 전달됨
  - 매개변수로 클래스 사용하면 input의 name과 동일한 프로퍼티에 setter로 값이 들어감


## 스프링 DB 접근 기술
### H2 데이터베이스 설치
1. 스프링 DB 접근 기술
  - JDBC : 애플리케이션 서버와 데이터베이스를 연결하는 기술
  - JdbcTemplate : 스프링이 제공하는 편리한 DB 연결 기술
  - JPA : 자동으로 쿼리를 생성해주는 기술
  - 스프링 데이터 JPA : JPA를 한번 더 감싼 기술
