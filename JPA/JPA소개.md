# JPA 소개
## 1. JPA란?
- Java Persistence API
- 자바 진영의 **ORM** 기술 표준

### 1.1. ORM
- Object-relational mapping(객체 관계 매핑)
- 객체는 객체답게, RDB는 RDB 답게 설계하고 ORM 프레임 워크가 중간에서 매핑하는 것
- 대중적인 언어에는 대부분 ORM 기술이 존재
- **ORM은 객체와 RDB 두 기둥 위에 있는 기술**

<br>

### 1.2. JPA 소개
***EJB 엔티티빈(자바표준) -> 하이버네이트(오픈소스) -> JPA(자바표준)***
- JPA는 표준 명세
    + JPA는 인터페이스의 모음
    + JPA 2.1 표준 명세를 구현한 3가지 구현체 : 하이버네이트, EclipseLink, DataNucleus

- JPA 버전
    + JPA 1.0(JSR 220) 2006년 : 초기 버전. 복합 키와 연관관계 기능이 부족
    + JPA 2.0(JSR 317) 2009년 : 대부분의 ORM 기능을 포함, JPA Criteria 추가
    + JPA 2.1(JSR 338) 2013년 : 스토어드 프로시저 접근, 컨버터(Converter), 엔티
티 그래프 기능이 추가



## 2. JPA의 필요성

### 2.1. 패러다임의 불일치
**[객체 vs 관계형 데이터베이스]**  
객체 지향 프로그래밍은 **추상화, 캡슐화, 정보은닉, 상속, 다형성** 등 시스템의 복잡성을 제어할 수 있는 다양한 장치들을 제공한다. 객체를 영구 보관하기 위해서는 RDB, NoSQL, File... 등 다양한 저장소를 이용할 수 있으나, 현실적으로는 관계형 데이터베이스를 이용하는 것이 압도적인 추세이다.  
그러나 객체를 RDB에 저장하기 위해서는 객체를 SQL 로 변환해야만 하는데, 이는 결국 개발자가 SQL 매퍼의 역할을 하는 데 많은 시간을 낭비하게 한다.

<br>

### 2.2. 객체와 관계형 데이터베이스의 차이   
1. 객체엔 상속의 개념이 있으나 RDB에는 상속의 개념이 없다. (비슷한 개념은 있으나 같은 것이 아님)  
    - 객체상의 자식 클래스 객체 생성시 SQL은 INSERT 문이 2개 필요하다. (테이블이 부모, 자식 둘로 쪼개졌기 때문)
    - 조회 역시 각각의 테이블에 따른 조인 SQL을 작성해야하는 등 복잡하기 때문에 DB에 저장할 객체에는 상속 관계를 쓰지 않는다.
2. **객체는 참조**를 사용하여 객체 끼리를 연관시키지만 **테이블은 외래키**를 사용한다. 객체의 연관관계는 단방향이지만 테이블은 양방향의 속성을 띈다.
3. 객체다운 모델링을 할 수록 매핑 작업이 늘어난다.
4. 진정한 의미의 **계층 분할이 어렵다**   
```java
/* DB에서 조회할 경우 */
String memberId = "100";
Member member1 = memberDAO.getMember(memberId);
Member member2 = memberDAO.getMember(memberId);
member1 == member2; //다르다.
class MemberDAO {
 
 public Member getMember(String memberId) {
 String sql = "SELECT * FROM MEMBER WHERE MEMBER_ID = ?";
 ...
 //JDBC API, SQL 실행
 return new Member(...);
 }
}
```
```java
/* 자바 컬렉션에서 조회할 경우 */
String memberId = "100";
Member member1 = list.get(memberId);
Member member2 = list.get(memberId);
member1 == member2; //같다.

```
<br>

### 2.3. JPA를 사용해야하는 이유
- SQL 중심적인 개발에서 객체 중심으로 개발
- 생산성 : 직접 SQL문을 작성하여 매핑할 필요 없음, 객체 대로 코딩하여 CRUD 가능
- 유지보수 : 필드 변경시 모든 SQL을 수정할 필요 없음, 필드만 추가하면 됨 (SQL은 JPA가 처리)
- 패러다임의 불일치 해결
- 성능
- 데이터 접근 추상화와 벤더 독립성
- 표준

