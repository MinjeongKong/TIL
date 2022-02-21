# @RequriedArgsConstructor


의존성 주입의 종류로는 `Constructor`, `Setter`, `Field` 타입이 있다.

### 1. Constructor(생성자)
```java
@Service
public class MemberListPrinter {

	private MemberDao memberDao;
	private MemberPrinter printer;

    @Autowired
    public MemberListPrinter(MemberDao memberDao, MemberPrinter printer){
        this.memberDao = memberDao;
        this.printer = printer;
    }

}
```

### 2. Setter
```java
public class MemberListPrinter {

	private MemberDao memberDao;
	private MemberPrinter printer;

    public MemberListPrinter() {
	}
	
	public MemberListPrinter(MemberDao memberDao, MemberPrinter printer) {
		this.memberDao = memberDao;
		this.printer = printer;
	}

    @Autowired
	public void setMemberDao(MemberDao memberDao) {
		this.memberDao = memberDao;
	}
	
	@Autowired
	public void setMemberPrinter(MemberSummaryPrinter printer) {
		this.printer = printer;
	}
}
```

### 3. Field
```java
@Service
public class UserService {

   @Autowired
   private UserRepository userRepository;
   
```
@Autowired를 이용한 의존성 주입을 필드 주입이라고 한다. 그러나 필드 주입은 스프링 팀에서 권장하는 방식은 아니라고 한다.

<br>

## @RequriedArgsConstructor 를 통한 생성자 주입
생성자주입의 단점은 위의 Constructor(생성자) 코드처럼 생성자를 만들기 번거롭다는 것이다. 하지만 이를 보완하기위해 `@RequriedArgsConstructor` 을 사용하여 간단한 방법으로 생성자 주입 방식의 코딩을 할 수 있다.

```java
@RequriedArgsConstructor
@Service
public class MemberListPrinter {

	private final MemberDao memberDao;
	private final MemberPrinter printer;

}
```

`@RequriedArgsConstructor` : final이 붙거나 @NotNull 이 붙은 필드의 생성자를 자동 생성해주는 롬복 어노테이션

### 생성자 주입의 장점
- 순환 참조 방지
- 테스트 코드 작성 용이
- 코드 악취 제거
- 객체 변이 방지 (final 가능)