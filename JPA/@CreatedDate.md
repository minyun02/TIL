# @CreatedDate
- 엔티티가 처음 생성될 때, 생성 날짜를 자동으로 설정하기 위해 사용되는 어노테이션이다.
- 일반적으로 @EntityListeners와 같이 사용된다.
- @EntityListeners를 가지고 있는 엔티티에 특정 이벤트가 발생할 때(저장, 업데이트 등) 특정 리스너 클래스를 호출하도록 설정한다
- 엔티티가 [[영속성 컨텍스트]]에 추가될 때 생성 날짜가 자동으로 설정된다.
- @SpringBootApplication이 있는 클래스에 @EnableJpaAuditing 어노테이션을 추가해야한다

```java
@Entity 
@EntityListeners(AuditingEntityListener.class) 
public class Entity { 

@Id 
@GeneratedValue(strategy = GenerationType.IDENTITY) 
private Long id;

@CreatedDate
@Column(updatable = false)
private LocalDateTime createdDate; 

}
```

# 동작 원리
- 애플리케이션이 시작하면 @EnableJpaAuditing 어노테이션이 JPA 감사 기능을 활성화한다
- 새로운 엔티티가 영속성 컨텍스트에 추가되면 @CreatedDate가 적용된 필드에 현재 날짜와 시간이 자동으로 설정된다
	- AuditingEntityListener가 이 작업을 처리한다