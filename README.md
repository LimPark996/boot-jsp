### ✅ 1. 왜 Spring Boot인가? (기존 Spring과 비교)

**🔸 기존 Spring의 문제점 (Spring Framework)**

**🔹 문제 1: 수동 설정 많음**

기존 Spring은 설정 XML이 많아서 진입 장벽이 높아요.

```xml
<!-- applicationContext.xml -->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
  <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
  ...
</bean>
```

**🔹 문제 2: 외부 서버 필요**

Tomcat 같은 WAS를 설치하고 war 배포해야 함 → 번거로움

---

**🔸 Spring Boot의 장점**

**✅ 자동 설정**

```java
@SpringBootApplication
public class BootJspApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootJspApplication.class, args);
    }
}
```

- 이 한 줄로 내장 Tomcat 구동 + ComponentScan + AutoConfig 끝
- 설정 파일도 `.yml` 하나로 통합

**✅ 내장 WAS (Tomcat)**

`java -jar` 명령만으로 서버가 바로 실행 → 빠른 배포 가능

**✅ 빌드 자동화 (Gradle + Spring Boot Plugin)**

```gradle
plugins {
  id 'org.springframework.boot' version '3.2.4'
}
```

**✅ 장점 정리**

| 항목 | 기존 Spring | Spring Boot |
|------|--------------|-------------|
| 서버 구동 | 외부 Tomcat 필요 | 내장 Tomcat 제공 |
| 설정 방식 | XML 중심 | Java 코드 & yml |
| 개발 속도 | 느림 | 빠름 |
| 자동 구성 | 없음 | 있음 (`@SpringBootApplication`) |

---

**✅ 기존 Spring의 문제점 (Spring Framework 기준)**

**🔹 문제 1: 수동 설정이 많다 = 설정을 개발자가 직접 다 해야 함**

**예) DB 연결하려면?**

기존 Spring에서는 아래와 같이 XML에 **모든 bean과 속성을 직접 정의**해야 했어요.

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

✔ 이렇게 일일이 작성해야 했던 항목들:
- 데이터베이스 커넥션 정보
- JPA 설정
- 트랜잭션 관리자
- 뷰 리졸버
- 컴포넌트 스캔 위치  
→ **항목이 많고, 중복이 많으며, 실수하기 쉬움**

---

**🔹 문제 2: 외부 WAS 설치 + war 배포 필수**

기존 Spring 프로젝트는 보통 **war 파일로 빌드**해서 **Tomcat에 수동 배포**해야 했어요.

1. Gradle/Maven으로 `war` 빌드  
2. 로컬 또는 운영 서버의 Tomcat에 `/webapps` 폴더에 war 파일 복사  
3. `server.xml`, `context.xml` 등 Tomcat 설정 수동 작성  
4. 서버를 재시작하거나 hot reload 구성

✔ 즉, Spring Framework는 **애플리케이션과 서버가 분리되어 있음**  
→ 개발자 입장에서 **배포 작업이 반복적으로 필요하고 느림**

---

**✅ Spring Boot의 개선점**

**✅ 1. 자동 설정 (Auto Configuration)**

Spring Boot는 `@SpringBootApplication` 어노테이션을 통해 아래의 설정을 자동화합니다.

```java
@SpringBootApplication
public class BootJspApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootJspApplication.class, args);
    }
}
```

**이 한 줄로 포함되는 설정들:**

| 내부 구성 요소 | 설명 |
|----------------|------|
| `@Configuration` | 자바 기반 설정 클래스 |
| `@EnableAutoConfiguration` | 클래스패스에 존재하는 라이브러리 기반 자동 설정 수행 |
| `@ComponentScan` | 현재 패키지 하위의 모든 @Component, @Service, @Controller 탐색 및 등록 |

---

**예) DB 설정 자동화**

**application.yml**
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

→ 별도의 XML 없이 이 설정만으로:
- `DataSource`, `EntityManagerFactory`, `TransactionManager` 등이 자동으로 생성됨

---

**✅ 2. 내장 WAS 제공**

Spring Boot는 Tomcat이 **내장 라이브러리**로 포함되어 있기 때문에  
**외부 Tomcat 설치 없이도 실행 가능**합니다.

**예) `build.gradle`**

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

→ 위 한 줄을 넣으면, Spring Boot는 `spring-boot-starter-web`에 포함된  
`tomcat-embed-core` 등 내장 Tomcat 의존성을 자동 추가합니다.

**실행은 이렇게만 하면 됨:**

```bash
./gradlew bootRun
# 또는
java -jar build/libs/myapp.jar
```

---

✔ 결과적으로:

| 항목 | Spring | Spring Boot |
|------|--------|-------------|
| Tomcat 설치 | 수동 설치 필요 | 불필요 (내장됨) |
| 배포 방식 | war 빌드 후 복사 | jar 실행 가능 |
| 설정 파일 | XML 기반 | yml/properties 기반 |
| 개발 속도 | 느림 | 빠름 (자동 설정) |

---

**✅ 3. 빌드 자동화**

Spring Boot는 Gradle(Maven도 가능)을 이용한 빌드 자동화를 지원하며,  
`spring-boot-gradle-plugin`을 통해 다음을 제공합니다:

- `bootRun`: 애플리케이션 실행
- `bootJar`: 실행 가능한 JAR 생성
- `bootBuildImage`: 도커 이미지 자동 생성

**Gradle 설정 예시**
```gradle
plugins {
    id 'org.springframework.boot' version '3.2.4'
    id 'io.spring.dependency-management' version '1.1.0'
}
```

✔ 위 설정만 하면 의존성 관리, 빌드, 실행 모두 자동화됨

---

**✅ 요약**

| 구분 | Spring (전통) | Spring Boot |
|------|---------------|--------------|
| 설정 방식 | XML, Java 설정 혼용 | Java + yml 파일 중심 |
| 서버 구성 | Tomcat 외부 설치 | 내장 Tomcat 자동 구동 |
| 실행 방식 | war → Tomcat 복사 | jar → 바로 실행 |
| 설정 코드량 | 많음 | 거의 없음 |
| 빌드 도구 | 수동 빌드 | Gradle/Maven 자동화 지원 |

---

## ✅ 2. JSP vs React (서버 vs 클라이언트 렌더링 구조 비교)

### 🔸 JSP: Server-Side Rendering

#### 구조 예시

```java
@Controller
public class MainController {
    @GetMapping("/list")
    public String showList(Model model) {
        model.addAttribute("items", repository.findAll());
        return "index"; // → /WEB-INF/views/index.jsp
    }
}
```

```jsp
<!-- index.jsp -->
<c:forEach var="item" items="${items}">
  <div>${item.name} - ${item.price}</div>
</c:forEach>
```

✔ 서버가 HTML을 생성하고 클라이언트에 전달 (전통적인 방식)

---

### 🔸 React: Client-Side Rendering (SPA)

#### 구조 예시

```jsx
useEffect(() => {
  fetch("/api/items")
    .then(res => res.json())
    .then(data => setItems(data));
}, []);
```

```java
@RestController
public class ItemApiController {
    @GetMapping("/api/items")
    public List<Item> getItems() {
        return repository.findAll();
    }
}
```

✔ 프론트는 React가, 백은 Spring Boot가 → API로 통신

---

### 🔸 장단점 비교

| 항목 | JSP | React |
|------|-----|--------|
| 렌더링 방식 | 서버에서 HTML 생성 | 브라우저에서 JS로 렌더링 |
| 초기 개발 난이도 | 낮음 | 높음 |
| 동적 기능 (검색, 클릭 등) | JavaScript 직접 추가 필요 | 매우 쉬움 (내장됨) |
| SEO 최적화 | 매우 유리 | 따로 설정 필요 |
| 백엔드 통합 | 자연스러움 (Model) | REST API 필요 |

---

### 💡 결론
- 빠른 결과 확인 & 전체 흐름 파악 → JSP
- 모던 웹, 유지보수, 대규모 시스템 → React

→ 유미님이 초반에 **Spring 흐름을 익히기엔 JSP**, 실무 준비는 React

---

## ✅ 3. JPA란? MyBatis와의 차이 & 코드 예시

### 🔸 MyBatis 구조

```xml
<select id="findAll" resultType="RealEstate">
  SELECT * FROM real_estate;
</select>
```

→ SQL 직접 작성 → 비즈니스 로직과 분리 어려움, 유지보수 비용 증가

---

### 🔸 JPA 구조 (ORM)

#### Entity 클래스
```java
@Entity
public class RealEstate {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String address;
}
```

#### Repository 인터페이스
```java
public interface RealEstateRepository extends JpaRepository<RealEstate, Long> {}
```

#### 컨트롤러 사용
```java
@Autowired
RealEstateRepository repository;

@GetMapping("/save")
public String save() {
    RealEstate house = new RealEstate();
    house.setName("서울집");
    house.setAddress("강남구");
    repository.save(house); // insert 자동 수행
    return "index";
}
```

---

### 🔸 장단점 비교

| 항목 | JPA | MyBatis |
|------|-----|---------|
| 쿼리 작성 | 자동 (CRUD 제공) | 수동 작성 |
| 개발 속도 | 빠름 | 느림 (XML 따로 관리) |
| 복잡한 쿼리 | 불편 (JPQL, QueryDSL) | 자유롭고 강력 |
| 유지보수 | 쉬움 | 어려움 |

→ CRUD만 한다면 **JPA가 간결하고 빠름**

---

## ✅ 4. Lombok: 반복 코드 제거

### 🔸 전통적 방식
```java
public class RealEstate {
    private String name;
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

### 🔸 Lombok 사용
```java
@Getter
@Setter
@NoArgsConstructor
public class RealEstate {
    private String name;
}
```

### 🔸 장점
- 코드 짧아지고 가독성 상승
- 컴파일 시 getter/setter 자동 생성됨

| 어노테이션 | 기능 |
|------------|------|
| `@Getter`, `@Setter` | getter/setter 생성 |
| `@NoArgsConstructor` | 기본 생성자 |
| `@AllArgsConstructor` | 모든 필드 생성자 |
| `@Data` | getter/setter, equals, toString 등 전체 포함 |

---

## ✅ 5. 어노테이션이란? Spring Boot와의 관계

### 🔸 정의
> 어노테이션(Annotation)은 클래스나 메서드에 부가 정보를 부여해서 프레임워크가 그 정보를 기반으로 동작하게 만드는 문법

---

### 🔸 Spring의 주요 어노테이션 역할

| 어노테이션 | 설명 | 역할 |
|------------|------|------|
| `@Controller` | MVC 컨트롤러 | 요청을 JSP로 전달 |
| `@RestController` | JSON 반환 | API 응답 컨트롤러 |
| `@Entity` | DB 테이블 매핑 | ORM 연결 |
| `@Repository` | DAO 선언 | 예외 처리 등 자동화 |
| `@Service` | 비즈니스 로직 | 트랜잭션 처리 등 |
| `@Autowired` | DI (의존성 주입) | 객체 자동 연결 |

---

### 🔸 Spring Boot에서는 어노테이션의 힘이 더 커짐
```java
@SpringBootApplication // 3가지 어노테이션 결합
→ @Configuration + @ComponentScan + @EnableAutoConfiguration
```

→ Spring Boot는 어노테이션을 통해 모든 설정을 자동화합니다.
