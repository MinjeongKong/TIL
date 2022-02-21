# @Qualifier
예전에 스프링으로 파일 업로드/다운로드 기능을 구현한 코드를 보다가 문득 @Qualifier 애노테이션이 붙어있는 것을 발견했는데 용도가 기억이 나질 않았다.

파일 관리를 위한 기본적인 인터페이스와 클래스를 다음과 같이 구현하였다.
```java
public interface StorageService {
    void init(Path path);

    String store(String uploadPath, MultipartFile file) throws Exception;

    public Resource load(String filePath) throws Exception;

    boolean delete(String filePath) throws IOException;
}
```
```java
@NoArgsConstructor
@Service
public class FileStorageService implements StorageService {

    @Value("${spring.servlet.multipart.location}")
    private String basePath;
    ...
}
```

서비스 코드에서 `FileStorageService` 를 의존 주입할 때의 코드는 다음과 같다.
```java
@RequiredArgsConstructor
@Service
public class UserService {

    @Qualifier("FileStorageService")
    private final StorageService storageService;
    ...
}
```
위는 `@RequiredArgsConstructor` 와 final 필드를 이용하여 생성자 주입으로 의존성을 받는다. 이 때 `@Qualifier("FileStorageService")` 는 왜 필요한가?

<br>

## @Qualifier 를 이용한 의존 객체 선택
자동 주입 가능한 빈이 두 개 이상이면 자동 주입할 빈을 지정할 수 있는 방법이 필요하다. 이 때 `@Qualifier` 를 사용한다

@Qualifier 는 두 위치에서 사용 가능하다. 첫 번째는 @Bean 을 붙인 빈 설정 메서드이다. 다음은 설정 예이다.

```java
@Configuration
public class AppCtx {

    ...
    @Bean
	@Qualifier("printer")
	public MemberPrinter memberPrinter1() {
		return new MemberPrinter();
	}

    @Bean
	public MemberPrinter memberPrinter2() {
		return new MemberPrinter();
	}
    ...
}
```
이 코드에서 memberPrinter1() 메서드에 "printer" 값을 갖는 @Qualifier 을 붙였다. 이 설정은 해당 빈의 한정 값으로 "printer"를 지정한다.

이렇게 지정한 한정 값은 @Autowired 에서 자동 주입할 빈을 한정할 때 사용한다. 다음이 @Qualifer 를 사용하는 두 번째 위치이다.
```java
public class MemberListPrinter {

	private MemberDao memberDao;
	private MemberPrinter printer;

    ...

    @Autowired
    @Qualifier("printer")
	public void setMemberPrinter(MemberPrinter printer) {
		this.printer = printer;
	}
}
```
`serMemberPrinter()` 메서드에 @Autowired 를 붙였으므로 MemberPrinter 타입의 빈을 자동 주입한다. 이 때 @Qualifier 값이 "printer" 이므로 한정 값이 "printer"인 빈을 의존 주입 후보로 사용한다.

@Autowired 를 필드와 메서드에 모두 적용할 수 있으므로 @Qualifer 도 필드와 메서드에 적용할 수 있다.

<br>

## 빈 이름과 기본 한정자
빈 설정에 @Qualifier 이 없으면 빈의 이름을 한정자로 지정한다.
```java
@Configuration
public class AppCtx2 {

	
	@Bean
	public MemberPrinter printer() {
		return new MemberPrinter();
	}
	
	@Bean
	@Qualifier("mprinter")
	public MemberPrinter printer2() {
		return new MemberPrinter();
	}
	
	@Bean
	public MemberInfoPrinter infoPrinter() {
		MemberInfoPrinter infoPrinter = new MemberInfoPrinter();
		return infoPrinter;
	}
}
```
여기서 printer() 메서드로 정의한 빈의 한정자는 빈 이름인 "printer"가 된다. printer2 빈은 @Qualifier 값인 "mprinter"가 한정자가 된다.

@Autowired 도 @Qualifier 이 없으면 필드나 파라미터 이름을 한정자로 사용한다.

|빈 이름|@Qualifier|한정자|
|---|---|---|
|printer||printer|
|printer2|mprinter|mprinter|
|infoPrinter||intoPrinter|

<br>

## 상위/하위 타입 관계와 자동 주입
그렇다면 `MemberPrinter` 클래스를 상속한 `MemberSummaryPrinter` 클래스가 빈에 등록 되면 어떨까?
```java
@Configuration
public class AppCtx {
    @Bean
	public MemberPrinter memberPrinter1() {
		return new MemberPrinter();
	}
	
	@Bean
	public MemberSummaryPrinter memberPrinter2() {
		return new MemberSummaryPrinter();
	}
}
```
이 경우에도 `MemberPrinter` 클래스 빈이 2개인 것과 같은 익셉션이 발생한다. 따라서 이 경우에도 @Qualifier 명시가 필요하다.