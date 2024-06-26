# 프록시란?

- 실제 객체를 감싸고 그 객체에 대한 접근을 제어하는 대리자 역할을 하는 객체다
- 프록시는 실제 객체 대신 클라이언트와 상호 작용하며, 필요할 때 실제 객체에 요청을 전달한다
- 스프링은 프록시로 트랜잭션 관리, 로깅, 보안 등 다양한 부가 기능을 제공한다
- 프록시는 스프링 AOP를 구현하는 핵심 기술이다
- 당연한 얘기지만, 빈으로 등록된 객체에만 프록시를 사용할 수 있다

# 프록시의 기본 개념

- 대리자 역할
    - 클라이언트의 요청을 받아서 실제 객체에 전달한다
    - 이 과정에서 요청을 가로채고, 추가적인 작업을 수행할 수 있다
- 투명성
    - 클라이언트는 프록시를 통해 실제 객체와 소통하지만 프록시 뒤에 실제 객체가 있다는 걸 모를 수도 있다
    - 프록시는 실제 객체와 동일한 인터페이스를 구현한다

# 프록시 종류

- JDK 동적 프록시
    - 인터페이스를 구현하는 프록시 객체를 생성한다
    - 인터페이스를 기반으로 프록시를 동적으로 생성한다
- CGLIB 프록시
    - 인터페이스 없이 클래스 상속으로 프록시를 생성한다
    - 실제 클래스를 상속하는 프록시 객체가 생성된다
    - Code Generation Library를 사용하여 실제 클래스를 상속받아 프록시 객체를 생성한다

# 프록시 동작 방식

- 빈 등록
    - 스프링 컨텍스트에 빈이 등록된다
    - 이때 @Transactional과 같은 어노테이션이 있는 경우, 스프링은 해당 빈에 대한 프록시 객체를 생성할 준비를 한다
- 프록시 생성
    - 빈 타입에 따라 JDK 동적 프록시나 CGLIB 프록시가 생성된다
- 프록시 반환
    - 클라이언트가 빈을 요청할 때 스프링은 실제 객체 대신 프록시 객체를 반환한다
- 클라이언트가 프록시 객체의 메서드를 호출

```java
TeamService teamService = applicationContext.getBean(TeamService.class);
teamService.deleteTeam(); //프록시 객체의 메서드 호출
```

- 프록시 객체가 메서드를 가로챈다
    - 호출을 가로채고, 추가적인 로직을 수행한다
    - 예를 들면, 트랜잭션을 시작한다

```java
public void deleteTeam() {
	// 트랜잭션 시작
	actualTeamService.deleteTeam();
	// 트랜잭션 커밋 혹은 롤백
}
```