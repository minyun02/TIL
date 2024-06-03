# 주입할 스프링 빈이 없어도 동작해야 하는 경우

- 3가지 방법이 있다
- @Autowired에 required false 옵션을 준다
	- false 옵션을 주면 해당 빈이 없는 경우에는 아래 메서드를 아예 실행 시키지 않는다

```java
@Autowired(required = false)
public void setNoBean1(Member noBean1) {
	콘솔 출력
}

```

- 주입 받아야하는 빈에 @Nullable 어노테이션을 달아준다
	- 빈이 없는 경우에는 null을 반환한다

```java
@Autowired
public void setNoBean1(@Nullable Member noBean1) {
	콘솔 출력
}

```

- 빈을 Optional로 감싸준다
	- 빈이 없는 경우 Optional.empty를 반환한다

```java
@Autowired(required = false)
public void setNoBean1(Optional<Member> noBean1) {
	콘솔 출력
}

```

# 빈이 없으면 어떤 예외가 발생할까?

- NoSuchBeanDefinitionException
	- 애플리케이션 컨텍스트에서 요청한 타입의 빈을 찾을 수 없을 때 발생하는 예외이다
	- 특정 타입이나 이름으로 빈을 요청했지만 해당 빈이 정으되지 않은 경우 발생한다

```java
public static void main(String[] args) {
	ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

	try {
		TeamService teamService = context.getBean(TeamService.class);
	} catch(NoSuchBeanDefinitionException e) {
		System.out.println("해당 빈을 찾을 수 없습니다: " + e.getMessage());
	}

}

```

- UnsatisfiedDependencyException
	- 생성자나 필드 주입 시 필요한 의존성을 주입 받을 수 없을 때 발생한다
	- 아래 코드에서처럼 생성자로 빈 주입을 받을 때 teamRepository 빈이 없다면 이 예외가 발생한다

```java
@Component
public class TeamService {
	private final TeamRepository teamReposotiry;

	@Autowired
	public TeamService(TeamRepository teamRepository) {
		this.teamRepository = teamRepository;
	}

}

```

- BeanCreationException
	- 빈 생성 중에 예외가 발생하면 이 예외가 던져진다
	- 발생 원인은 다양하다
- NoUniqueBeanDefinitionException
	- 요청한 타입의 빈이 두 개 이상 존재하여 어떤 빈을 주입해야 하는지 결정할 수 없을 때 발생한다