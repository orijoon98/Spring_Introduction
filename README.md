# 스프링 입문

## 강의

[[무료] 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 - 인프런 | 강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)

## 정리

### 프로젝트 생성

- 스프링 부트 스타터 사이트로 이동해서 스프링 프로젝트 생성

[Spring Initializr](https://start.spring.io/)

- build.gradle 파일에서 라이브러리 관리

```java
plugins {
      id 'org.springframework.boot' version '2.3.1.RELEASE'
      id 'io.spring.dependency-management' version '1.0.9.RELEASE'
      id 'java'
}

group = 'hello'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
      mavenCentral()
}

dependencies {
			implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
      implementation 'org.springframework.boot:spring-boot-starter-web'
      testImplementation('org.springframework.boot:spring-boot-starter-test') {
			      exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
      }
}

test {
			useJUnitPlatform()
}
```

### 빌드하고 실행하기

```bash
./gradlew build

cd build/libs

java -jar hello-spring-0.0.1-SNAPSHOT.jar
```

- 빌드를 하면 파일 하나로 서버를 실행할 수 있다

### 스프링 웹 개발 기초

- 정적 컨텐츠
    - static 폴더에 html 파일 생성하면 바로 실행 가능

- MVC와 템플릿 엔진
    - MVC: Model, View, Controller

```java
@Controller
  public class HelloController {
      @GetMapping("hello-mvc")
      public String helloMvc(@RequestParam("name") String name, Model model) {
          model.addAttribute("name", name);
          return "hello-template";
      }
}
```

- API

```java
@Controller
  public class HelloController {
      @GetMapping("hello-api")
      @ResponseBody
      public Hello helloApi(@RequestParam("name") String name) {
          Hello hello = new Hello();
          hello.setName(name);
          return hello;
      }
      static class Hello {
          private String name;
          public String getName() {
              return name;
					}
          public void setName(String name) {
              this.name = name;
					} 
			}
}
```

### 테스트

- 테스트를 작성하는 것이 매우 중요
- 전체 개발의 60% 이상을 테스트에 투자할 정도로 중요함
- @AfterEach : 각 테스트가 종료될 때 마다 이 기능을 실행

```java
@AfterEach
public void afterEach() {
    memberRepository.clearStore();
}
```

### 스프링 빈과 의존관계

- 컴포넌트 스캔과 자동 의존관계 설정
    - @Component Annotation이 있으면 스프링 빈으로 자동 등록된다.
    - @Component를 포함하는 다음 Annotation도 스프링 빈으로 자동 등록된다.
        - @Controller
        - @Service
        - @Repository
    - 생성자에 @Autowired를 사용하면 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입한다. 생성자가 1개만 있으면 @Autowired는 생략할 수 있다.
    - 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 싱글톤으로 등록한다(유일하게 하나만 등록해서 공유한다) 따라서 같은 스프링 빈이면 모두 같은 인스턴스다. 설정으로 싱글톤이 아니게 설정할 수 있지만, 특별한 경우를 제외하면 대부분 싱글톤을 사용한다.

- 자바 코드로 직접 스프링 빈 등록하기

```java
@Configuration
public class SpringConfig {
	@Bean
  public MemberService memberService() {
		return new MemberService(memberRepository());
  }
  @Bean
  public MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }
}

```

### 스프링 DB 접근 기술

- 순수 Jdbc
- 스프링 통합 테스트
- 스프링 JdbcTemplate
- JPA
- 스프링 데이터 JPA

### AOP 적용

- AOP: Aspect Oriented Programming
- 공통 관심 사항, 핵심 관심 사항 분리

```java
@Component
@Aspect
public class TimeTraceAop {

	 @Around("execution(* hello.hellospring..*(..))")
	
	 public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
	
		  long start = System.currentTimeMillis();
		
		  System.out.println("START: " + joinPoint.toString());
		
		  try {
				  return joinPoint.proceed();
		  } finally {
				  long finish = System.currentTimeMillis();
				  long timeMs = finish - start;
				  System.out.println("END: " + joinPoint.toString()+ " " + timeMs + "ms");
		  }
   }
}  
```
