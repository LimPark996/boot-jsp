### ✅ 1. 왜 Spring Boot인가? (기존 Spring과 비교)

**✅ 기존 Spring의 문제점 (Spring Framework 기준)**

##### 🔹 문제 1: 수동 설정이 많다 = 설정을 개발자가 직접 다 해야 함

<sup>기존 Spring에서는 아래와 같이 XML에 **모든 bean과 속성을 직접 정의**해야 했어요.</sup>

```xml
<!-- applicationContext.xml -->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/test"/>
    <property name="username" value="root"/>
    <property name="password" value="password"/>
</bean>

<bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    ...
</bean>
```

<sup>✔ 이렇게 일일이 작성해야 했던 항목들:</sup><br>
<sup> 데이터베이스 커넥션 정보</sup><br>
<sup> JPA 설정</sup><br>
<sup> 트랜잭션 관리자</sup><br>
<sup> 뷰 리졸버</sup><br>
<sup> 컴포넌트 스캔 위치</sup><br> 
<sup> → **항목이 많고, 중복이 많으며, 실수하기 쉬움**</sup><br>

---

##### 🔹 문제 2: 외부 WAS 설치 + war 배포 필수

<sup>기존 Spring 프로젝트는 보통 **war 파일로 빌드**해서 **Tomcat에 수동 배포**해야 했어요.</sup><br>

<sup>Gradle/Maven으로 `war` 빌드</sup><br>
<sup>로컬 또는 운영 서버의 Tomcat에 `/webapps` 폴더에 war 파일 복사</sup><br>
<sup>`server.xml`, `context.xml` 등 Tomcat 설정 수동 작성</sup><br>
<sup>서버를 재시작하거나 hot reload 구성</sup><br>

<sup>✔ 즉, Spring Framework는 **애플리케이션과 서버가 분리되어 있음**</sup><br>
<sup>→ 개발자 입장에서 **배포 작업이 반복적으로 필요하고 느림**</sup><br>

---
##### 🔹 [질문 1] 외부 WAS 설치 + war 배포가 뭔 뜻인지 모르겠어요

<sup>👉 용어부터 설명할게요:</sup><br>
<sup>WAS: Web Application Server, 웹 어플리케이션을 실행해주는 서버</sup><br>
<sup>대표적인 게 Tomcat (Java 진영에서 거의 기본)</sup><br>

<sup>🔸 기존 Spring Framework에서는…</sup><br>
<sup>Spring 코드만 가지고는 웹서버에 띄울 수 없어요.</sup><br>
<sup>따라서 Tomcat 같은 서버를 별도로 설치하고,</sup><br>
<sup>거기에 프로젝트를 넣어줘야 합니다.</sup><br>

<sup>🔧 실제 작업 순서 (기존 Spring 기준)</sup><br>
<sup>Spring 프로젝트 코드를 .war 파일로 만든다 → war = 웹 어플리케이션 압축 파일</sup><br>
<sup>이 war 파일을 Tomcat의 /webapps 폴더에 복사한다.</sup><br>
<sup>Tomcat의 server.xml, context.xml 등을 수정해 경로 설정한다.</sup><br>
<sup>Tomcat 서버를 수동으로 껐다 켠다. (또는 설정해서 자동 reload)</sup><br>

<sup>즉, 서버와 코드가 따로 노는 구조</sup><br>

<sup>→ Spring은 코드만 있고, 실행하려면 Tomcat이 따로 있어야 함</sup><br>

<sup>🔸 Spring Boot는 어떻게 다르냐?</sup><br>
<sup>Spring Boot는 tomcat-embed-core 같은 내장 Tomcat 라이브러리를 포함하고 있어서, 코드 안에 서버가 같이 들어 있어요.</sup><br>
<sup>즉, 별도로 Tomcat을 설치하지 않고도, 바로 실행 가능:</sup><br>

```bash
java -jar build/libs/myapp.jar
```
<sup>→ 이 jar 파일은 Spring Boot + Tomcat + 내 코드가 모두 들어 있는 실행 가능 패키지예요.</sup><br>

---

**✅ Spring Boot의 개선점**

##### ✅ 1. 자동 설정 (Auto Configuration)

<sup>Spring Boot는 `@SpringBootApplication` 어노테이션을 통해 아래의 설정을 자동화합니다.</sup><br>

```java
@SpringBootApplication
public class BootJspApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootJspApplication.class, args);
    }
}
```

<sup>이 한 줄로 포함되는 설정들:</sup><br>

<sup>| 내부 구성 요소 | 설명 |</sup><br>
<sup>|----------------|------|</sup><br>
<sup>| `@Configuration` | 자바 기반 설정 클래스 |</sup><br>
<sup>| `@EnableAutoConfiguration` | 클래스패스에 존재하는 라이브러리 기반 자동 설정 수행 |</sup><br>
<sup>| `@ComponentScan` | 현재 패키지 하위의 모든 @Component, @Service, @Controller 탐색 및 등록 |</sup><br>

---

<sup>예) DB 설정 자동화</sup><br>

<sup>application.yml
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
```

<sup>→ 별도의 XML 없이 이 설정만으로:</sup><br>
<sup>`DataSource`, `EntityManagerFactory`, `TransactionManager` 등이 자동으로 생성됨</sup><br>

---

##### ✅ 2. 내장 WAS 제공

<sup>Spring Boot는 Tomcat이 **내장 라이브러리**로 포함되어 있기 때문에</sup><br>
<sup>**외부 Tomcat 설치 없이도 실행 가능**합니다.</sup><br>

<sup>예) `build.gradle`</sup><br>

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

<sup>→ 위 한 줄을 넣으면, Spring Boot는 `spring-boot-starter-web`에 포함된</sup><br>  
<sup>`tomcat-embed-core` 등 내장 Tomcat 의존성을 자동 추가합니다.</sup><br>

<sup>실행은 이렇게만 하면 됨:</sup><br>

```bash
./gradlew bootRun
# 또는
java -jar build/libs/myapp.jar
```

---

<sup>✔ 결과적으로:</sup><br>

<sup>| 항목 | Spring | Spring Boot |</sup><br>
<sup>|------|--------|-------------|</sup><br>
<sup>| Tomcat 설치 | 수동 설치 필요 | 불필요 (내장됨) |</sup><br>
<sup>| 배포 방식 | war 빌드 후 복사 | jar 실행 가능 |</sup><br>
<sup>| 설정 파일 | XML 기반 | yml/properties 기반 |</sup><br>
<sup>| 개발 속도 | 느림 | 빠름 (자동 설정) |</sup><br>

---

##### ✅ 3. 빌드 자동화

<sup>Spring Boot는 Gradle(Maven도 가능)을 이용한 빌드 자동화를 지원하며,</sup><br>
<sup>`spring-boot-gradle-plugin`을 통해 다음을 제공합니다:</sup><br>

<sup>`bootRun`: 애플리케이션 실행</sup><br>
<sup>`bootJar`: 실행 가능한 JAR 생성</sup><br>
<sup>`bootBuildImage`: 도커 이미지 자동 생성</sup><br>

<sup>Gradle 설정 예시</sup><br>

```gradle
plugins {
    id 'org.springframework.boot' version '3.2.4'
    id 'io.spring.dependency-management' version '1.1.0'
}
```

<sup>✔ 위 설정만 하면 의존성 관리, 빌드, 실행 모두 자동화됨</sup><br>

---

**✅ Spring Boot에서 Docker 반드시 필요한 상황**

##### 🟠 상황 1: 운영 서버에 직접 배포해야 할 때

<sup>예시</sup><br>
<sup>회사 리눅스 서버에 올려야 한다거나</sup><br>
<sup>클라우드(GCP, AWS EC2) 서버에 배포해야 할 때</sup><br>

<sup>왜 Docker가 필요함?</sup><br>
<sup>내 개발 환경(JDK, 포트 설정 등)을 그대로 묶어서 서버에 보내야 함</sup><br>
<sup>서버에 Java가 깔려있는지, 설정이 맞는지 하나하나 확인할 수 없음</sup><br>

<sup>어떻게 하나요?</sup><br>
<sup>`Dockerfile`을 작성해서</sup><br>
<sup>내 코드를 포함한 실행 환경을 **컨테이너 이미지로 만듦**</sup><br>
<sup>서버에서 `docker run`으로 실행</sup><br>

---

##### 🟠 상황 2: 팀원들과 개발 환경을 맞추고 싶을 때

<sup>예시</sup><br>
<sup>유미님은 Java 17, 팀원은 Java 11 → 빌드 에러</sup><br>
<sup>내 PC에는 MySQL 8.0, 다른 팀원은 SQLite → 동작 안 함</sup><br>

<sup>Docker를 쓰면?</sup><br>
<sup>팀원이 코드 내려받고 `docker-compose up`만 하면</sup><br>
<sup>Java 버전, MySQL 버전, 포트 설정까지 완전 똑같아짐</sup><br>  
<sup>→ **환경 통일**, 에러 줄어듦</sup><br>

---

##### 🟠 상황 3: 여러 개의 서비스를 동시에 실행하고 싶을 때

<sup>예시</sup><br>
<sup>Spring Boot 백엔드</sup><br>
<sup>React 프론트엔드</sup><br>
<sup>MySQL 데이터베이스</sup><br>  
<sup>→ 이걸 **하나의 프로젝트처럼 동시에 실행하고 싶을 때**</sup><br>

<sup>Docker로 어떻게 하나요?</sup><br>
<sup>`docker-compose.yml` 하나에 3개 컨테이너 정의</sup><br>
<sup>`docker-compose up` 한 줄로 백+프론트+DB 실행됨</sup><br>

---

##### ✅ 질문: 왜 React + Spring Boot + MySQL을 하나의 Docker로 묶어야 할까?

<sup>→ 정답: 이 3개는 독립적인 서버이기 때문에 실행, 연결, 설정을 자동화해서 함께 움직이게 하려는 목적이에요.</sup><br>

<sup>🎯 상황 설명: 개발 환경에서 필요한 구성</sup><br>
<sup>예를 들어 유미님이 이런 프로젝트를 만든다고 해봐요:</sup><br>

```yaml
📦 my-fullstack-project
├── backend/      ← Spring Boot (포트 8080)
├── frontend/     ← React (포트 3000)
├── docker-compose.yml
```

<sup>이때, 개발자가 하나하나 수동으로 실행하면 아래처럼 해야 돼요:</sup><br>

<sup>❌ Docker 없이 하는 방식:</sup><br>
<sup>백엔드: cd backend && ./gradlew bootRun</sup><br>

<sup>프론트: cd frontend && npm start</sup><br>

<sup>MySQL: 로컬에 설치해놓고 실행 (버전 맞추기도 어려움)</sup><br>

<sup>👉 불편하고, 환경이 서로 달라서 연결도 수동</sup><br>

---

<sup>🟠 상황 4: CI/CD로 자동 배포할 때</sup><br>

<sup>예시</sup><br>
<sup>GitHub에 push하면 자동으로 배포되게 하고 싶을 때</sup><br>

<sup>Docker를 쓰는 이유</sup><br>
<sup>어떤 환경에서도 **일관된 실행 결과를 보장**</sup><br>
<sup>이미지 한 번 만들면 어디서든 실행 가능</sup><br>

<sup>도구 예시</sup><br>
<sup>GitHub Actions → Docker 이미지 만들고 DockerHub에 업로드 → 서버에서 pull & run</sup><br>

---

<sup>🟠 상황 5: 플랫폼이 Docker만 지원할 때</sup><br>

<sup>예시</sup><br>
<sup>Google Kubernetes Engine(GKE), AWS ECS 같은 플랫폼은 **Docker 이미지만 받음**</sup><br> 
<sup>jar 파일은 못 쓰고, 컨테이너로 포장된 실행파일만 가능함</sup><br>

---

##### ✅ Docker가 필요 없는 상황

<sup>🟢 상황 1: 혼자 개발할 때 (로컬 개발)</sup><br>

<sup>예시</sup><br>
<sup>유미님이 Spring Boot 프로젝트 혼자 만들고 `localhost:8080`에서 확인만 하고 싶을 때</sup><br>

<sup>Docker 없이도 가능?</sup><br>
<sup>✔ 가능. 그냥 이렇게만 하면 돼요:</sup><br>

```bash
./gradlew bootRun
# 또는
java -jar build/libs/myapp.jar
```

<sup>→ Docker 없이도 완벽하게 개발 가능</sup><br>

---

<sup>🟢 상황 2: Render, Railway, Heroku 등 자동 배포 서비스 이용할 때</sup><br>

<sup>예시</sup><br>
<sup>유미님이 GitHub에 코드를 올려두고</sup><br>
<sup>Render가 자동으로 빌드해서 jar 실행까지 해주는 경우</sup><br>

<sup>Docker 없이 가능한 이유?</sup><br>
<sup>Render 내부에서 알아서 `./gradlew build` 실행하고 `java -jar`도 해줌</sup><br>
<sup>우리가 Dockerfile을 써서 컨테이너를 만들 필요 없음</sup><br>

---

<sup>🟢 상황 3: 정적 웹만 있을 때 (React 결과물, HTML 등)

<sup>예시</sup><br>
<sup>React를 빌드해서 나온 `/build` 폴더에 HTML, JS만 있을 때</sup><br>
<sup>또는 HTML/CSS만 있는 페이지</sup><br>

<sup>왜 Docker 필요 없음?</sup><br>
<sup>이건 웹서버 없이도 GitHub Pages, Netlify 같은 정적 호스팅에 올리면 끝이에요</sup><br>

<sup>→ Spring Boot, 서버 실행 필요 X → Docker 필요 없음</sup><br>

---
---

##### ✅ 2. JSP vs React (서버 vs 클라이언트 렌더링 구조 비교)

<sup>🔸 JSP: Server-Side Rendering</sup><br>

<sup> 구조 예시</sup><br>

```java
@Controller  // 이 클래스는 클라이언트 요청을 처리하는 Spring MVC 컨트롤러임을 명시
public class MainController {

    @GetMapping("/list")  // "/list"라는 경로로 GET 요청이 들어오면 아래 메서드 실행
    public String showList(Model model) {
        // findAll() 메서드를 호출해서 DB에서 모든 아이템을 가져옴 (JPA Repository에서 제공)
        model.addAttribute("items", repository.findAll());  
        // 뷰 이름 "index"를 반환 → 설정된 ViewResolver에 의해 "/WEB-INF/views/index.jsp"로 연결됨
        return "index";
    }
}
```

```jsp
<!-- index.jsp -->
<c:forEach var="item" items="${items}">
// items="${items}": 컨트롤러에서 model.addAttribute("items", ...)로 전달된 리스트를 받음
// var="item": 리스트 안의 각각 요소를 item으로 하나씩 꺼냄
  <div>${item.name} - ${item.price}</div>
// ${item.name}: 객체 item의 name 필드 값 출력
// ${item.price}: price 필드 값 출력
// 즉, DB에서 가져온 각 객체의 이름과 가격을 <div> 안에 보여줌
</c:forEach>
// forEach 반복문 종료
```

<sup> ✔ 서버가 HTML을 생성하고 클라이언트에 전달 (전통적인 방식)</sup><br>

```bash
1. 사용자가 /list 주소로 접속
2. Spring Boot가 MainController.showList() 실행
3. repository.findAll()로 DB에서 데이터 가져옴
4. 데이터를 "items"라는 이름으로 JSP에 전달
5. JSP에서 <c:forEach>로 하나씩 꺼내서 화면에 출력
```

---

<sup>🔸 React: Client-Side Rendering (SPA)</sup><br>

<sup> 구조 예시</sup><br>

```jsx
useEffect(() => {
// React 컴포넌트가 마운트(처음 화면에 렌더링) 될 때 실행되는 코드 블록 시작
// useEffect는 Side Effect (예: 서버 요청, 타이머 등) 실행용 훅
  fetch("/api/items")
// 현재 주소 기준으로 /api/items 경로로 GET 요청 보냄
// 이건 Spring Boot 서버에게 데이터를 달라고 요청하는 것
    .then(res => res.json())
// 응답(response)을 JSON 형식으로 파싱
// .json()은 JSON 응답 본문을 JS 객체로 변환함
    .then(data => setItems(data));
// 변환된 데이터를 setItems()로 저장
// setItems는 보통 useState()로 정의된 상태 업데이트 함수
// 이걸 호출하면 화면이 다시 렌더링되며 데이터가 화면에 표시됨
}, []);
// []는 의존성 배열이 비어 있으므로 한 번만 실행됨 (컴포넌트 mount 시)
```

```java
@RestController
// 이 코드는 "데이터(API)를 반환하는 컨트롤러"예요.
// 즉, HTML 화면이 아니라 JSON 데이터를 응답으로 주는 컨트롤러입니다.
public class ItemApiController {
// Spring Boot에서 요청을 처리하는 클래스 선언
    @GetMapping("/api/items")
// 클라이언트(예: React)가 /api/items 경로로 GET 요청을 보내면
// 이 메서드가 실행됨
    public List<Item> getItems() {
// List<Item> 타입의 데이터를 반환하겠다는 뜻
// Spring Boot가 이 리스트를 JSON 배열로 자동 변환해줌
        return repository.findAll();
// repository 객체를 사용해서 DB에서 모든 아이템을 조회함
// findAll()은 JPA의 기본 메서드로, 전체 레코드를 가져옴
    }
}
```

<sup> ✔ 프론트는 React가, 백은 Spring Boot가 → API로 통신</sup><br>

```bash
1. React가 브라우저에서 /api/items로 GET 요청 보냄
2. Spring Boot의 ItemApiController.getItems()가 실행됨
3. DB에서 Item 데이터 목록을 가져와 JSON으로 변환해 응답
4. React는 그 응답을 받아서 setItems(data)로 상태 저장
5. 컴포넌트는 새로운 데이터로 다시 렌더링됨
```

<sup> 추가]
<sup> **@Controller**는 뷰 이름(JSP, HTML)을 반환하는 컨트롤러
<sup> return 값은 "파일 이름"으로 인식돼서 뷰 리졸버가 JSP를 찾아감
<sup> **@ResponseBody**는 return 값이 **그대로 HTTP 응답 본문(body)**에 들어가게 함
<sup> 즉, return "Hello!"는 HTML 화면이 아니라 그냥 문자열 출력함
<sup> **직렬화**는 Java 객체 → JSON 문자열로 변환함
<sup> **컴포넌트**는 화면(UI)을 구성하는 "작고 독립적인 조각"
<sup> 화면을 여러 조각으로 나눠서, 각 조각을 컴포넌트라는 단위로 개발함
<sup> 마치 HTML 요소(예: <header>, <footer>, <nav>)처럼, 하지만 기능 + 스타일 + 로직까지 포함된 블록임
<sup> **렌더링**은 "컴포넌트를 화면에 그리는 과정"
<sup> 즉, 컴포넌트 코드를 보고 그걸 브라우저가 이해해서 화면에 HTML로 보여주는 것
  
---

<sup>🔸 장단점 비교</sup><br>

<sup>| 항목 | JSP | React |</sup><br>
<sup>|------|-----|--------|</sup><br>
<sup>| 렌더링 방식 | 서버에서 HTML 생성 | 브라우저에서 JS로 렌더링 |</sup><br>
<sup>| 초기 개발 난이도 | 낮음 | 높음 |</sup><br>
<sup>| 동적 기능 (검색, 클릭 등) | JavaScript 직접 추가 필요 | 매우 쉬움 (내장됨) |</sup><br>
<sup>| SEO 최적화 | 매우 유리 | 따로 설정 필요 |</sup><br>
<sup>| 백엔드 통합 | 자연스러움 (Model) | REST API 필요 |</sup><br>

---
---

##### ✅ 3. JPA란? MyBatis와의 차이 & 코드 예시

<sup>🔸 MyBatis 구조**</sup><br>

```xml
<select id="findAll" resultType="RealEstate">
  SELECT * FROM real_estate;
</select>
```

<sup>→ SQL 직접 작성 → 비즈니스 로직과 분리 어려움, 유지보수 비용 증가</sup><br>

---

<sup>🔸 JPA 구조 (ORM)</sup><br>

<sup>Entity 클래스

```java
@Entity  // 이 클래스는 DB 테이블로 쓰겠다고 JPA에 알려주는 어노테이션
public class RealEstate {

    @Id  // 기본 키(Primary Key) 설정
    @GeneratedValue(strategy = GenerationType.IDENTITY) // 자동 증가 설정 (MySQL auto_increment)
    private Long id;

    private String name;    // 부동산 이름 → DB의 name 컬럼
    private String address; // 주소 → DB의 address 컬럼
}
```

<sup>왜 @Entity를 써야 해?

<sup>이 클래스가 DB의 테이블로 자동 생성되거나 연결되게 해주기 위해서예요.
<sup>@Entity가 없으면 JPA는 이게 그냥 자바 클래스인지 DB랑 연결할 클래스인지 몰라요.

<sup>왜 @Id를 써야 해?

<sup>테이블의 **기본키(Primary Key)**가 뭔지를 지정해야 insert/update가 가능해요.

<sup>Repository 인터페이스

```java
public interface RealEstateRepository extends JpaRepository<RealEstate, Long> {}
```

<sup>JpaRepository<엔티티 클래스, 기본키 타입>을 상속하면, 아래와 같은 기능을 자동으로 쓸 수 있어요:

```java
repository.save(객체);          // insert or update
repository.findAll();          // SELECT * FROM 테이블
repository.findById(id);       // SELECT ... WHERE id=?
repository.deleteById(id);     // DELETE FROM ...
```

<sup>왜 이렇게 나눴냐?
<sup>DB 접근 코드(SQL 같은 것)를 다 여기서 하게 해서
<sup>Controller에서는 DB 코드 없이 로직만 쓰게 하려고!

<sup>컨트롤러 사용

```
@Controller
public class RealEstateController {

    @Autowired
    RealEstateRepository repository;

    @GetMapping("/save")
    public String save() {
        RealEstate house = new RealEstate();
        house.setName("서울 아파트");
        house.setAddress("강남구");
        repository.save(house);  // 진짜 DB에 저장됨
        return "index";
    }
}
```

<sup>✔ 여기는 오직 "서울 아파트를 저장하자"는 행위만 담당

---
---

##### ✅ 4. Lombok: 반복 코드 제거

<sup>✅ 질문: Lombok이 뭔데?

<sup>Lombok은 자바 코드에서 반복되는 getter, setter, 생성자 코드를 자동으로 만들어주는 도구예요.
<sup>직접 쓰지 않아도, 컴파일할 때 자동으로 만들어줌

<sup>🟥 ❌ Lombok 없이: (순수 Java)**

```java
public class RealEstate {
    private String name;
    
    // 생성자
    public RealEstate() {}

    // getter
    public String getName() {
        return name;
    }

    // setter
    public void setName(String name) {
        this.name = name;
    }
}
```
<sup>✔ 이걸 쓰면 이름을 가져오거나 바꿀 때 이렇게 써야 해요:

```java
RealEstate r = new RealEstate();
r.setName("서울집");             // 값 넣기
System.out.println(r.getName()); // 값 꺼내기
```

<sup>✅ Lombok 쓰면 이렇게 짧아져요**

```java
@Getter                // getter 자동 생성
@Setter                // setter 자동 생성
@NoArgsConstructor     // 기본 생성자 자동 생성
public class RealEstate {
    private String name;
}
```
<sup>이렇게만 써도, 실제로는 다음 코드가 컴파일할 때 자동으로 생김:

```java
public class RealEstate {
    private String name;

    public RealEstate() {}

    public String getName() { return name; }

    public void setName(String name) { this.name = name; }
}
```

<sup>✔ 내가 직접 쓰지 않아도 컴파일할 때 자동으로 추가됨
<sup>→ 코드가 훨씬 짧아지고 깔끔해져요

```bash
@Getter, @Setter를 붙이면,
getter/setter 메서드를 직접 쓰지 않아도,
마치 있는 것처럼 객체.get필드명() / 객체.set필드명() 사용이 가능합니다.
```
---

<sup>🔹 기본 생성자: @NoArgsConstructor
```java
@NoArgsConstructor
public class RealEstate {
    private String name;
}
```
<sup>✔ 이 어노테이션은 이 코드를 자동 생성해요:

```java
public RealEstate() {
    // 아무 필드도 받지 않는 기본 생성자
}
```
<sup>✅ 사용 예
```java
RealEstate r = new RealEstate(); // 가능!
r.setName("서울집");
```

<sup>🔹 전체 필드 생성자: @AllArgsConstructor
```java
@AllArgsConstructor
public class RealEstate {
    private String name;
}
```
<sup>✔ 이건 이 코드를 자동 생성해요:

```
java
public RealEstate(String name) {
    this.name = name;
}
```

<sup>✅ 사용 예
```java
RealEstate r = new RealEstate("서울집"); // 필드 하나를 한 번에 세팅 가능
```

<sup>🔹 “전체 자동 생성” = @Data 어노테이션

```java
@Data
public class RealEstate {
    private String name;
}
```
<sup>→ 이 한 줄은 아래 전부를 자동으로 만듭니다:

<sup>| 어노테이션               | 자동 생성되는 메서드                          |
<sup>|--------------------------|----------------------------------------------|
<sup>| `@Getter`                | `get필드명()`                                |
<sup>| `@Setter`                | `set필드명(값)`                              |
<sup>| `@ToString`              | `toString()`                                 |
<sup>| `@EqualsAndHashCode`     | `equals(Object o)`, `hashCode()`             |
| `@RequiredArgsConstructor` | `생성자(final 필드만 받는)`               |
