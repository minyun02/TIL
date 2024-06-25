# Soft Delete vs Hard Delete
- soft delete(논리 삭제)
    - 실제 데이터를 삭제하는게 아니라 삭제용 칼럼을 두고 그 값을 변경해서 논리적으로 삭제를 구현하는 방식이다
    - 주로 isDeleted와 같은 boolean 필드를 만들거나 삭제 날짜 필드를 사용한다
    - 장점
        - 데이터 복구에 용이하다
        - 삭제된 데이터 이력을 유지할 수 있다
        - 관련 데이터의 참조 무결성을 유지하기 쉽다
        - 실수로 영구적 데이터 손실을 방지할 수 있다
    - 단점
        - 데이터베이스 크기가 계속 커진다
        - 인덱스 효율이 떨어질 수 있다
        - 실제 삭제를 위한 추가 작업이 필요할 수 있다
- hard delete(물리 삭제)
    - 실제 데이터를 테이블에서 삭제하는 방식이다
    - 장점
        - 데이터베이스 크기를 효율적으로 관리할 수 있다
        - 쿼리가 단순하고 직관적이다
        - 일반적으로 데이터베이스 성능이 더 좋다
    - 단점
        - 삭제한 데이터 복구가 어렵거나 불가능하다
        - 삭제 이력을 추적할 수 없다
        - 관련 데이터의 무결성을 유지하기 어렵다
- 뭘 설택하는게 좋나?
    - 데이터의 중요성과 복구 필요성을 판단한다
    - 데이터베이스 크기와 관리 용이성

# JPA로 어떻게 Soft Delete를 어떻게 구현할까?
- @SQLDelete, @Where
    - @SQLDelete(sql = "삭제 쿼리")
    - 데이터를 삭제하는 경우에 @SQLDelete의 sql 옵션으로 지정해놓은 쿼리가 나간다
    - 여기서 soft delete에 사용하는 필드값을 변경해주는 update 문을 작성해서 soft delete를 구현한다
    - 그리고 데이터를 조회하는 경우에는 삭제된 데이터를 제외하고 조회하는 조건을 기본값으로 지정할 수 있다
    - 이때 사용하는 애너테이션이 @Where이다
    - @Where(clause = "isDeleted = false") 이렇게 삭제되지 않은 데이터를 조회하는 조건을 걸어둔다
    - @SQLRestriction 애너테이션은 하이버네이트 5.4 버전 이상부터 @Where을 대신해서 사용된다
    - @SQLRestriction("isDeleted = false") 이렇게 사용한다
    - 모든 조회 쿼리에 조건이 붙어서 나가기 때문에 만약에 삭제된 데이터를 조회해야하는 경우가 생기면 네이티브 쿼리를 이용하는 등, 다른 방법이 필요하다
```java
@Entity
@SQLDelete(sql = "UPDATE table SET isDeleted = true WHERE id = ?")
@SQLRestriction("isDeleted = false")
public class SampleEntity() {
	...
}
```
- @PreRemove
    - 엔티티가 삭제되기 전에 호출되는 애너테이션이다
    - 삭제되전에 수행되어야하는 로직에 이 애너테이션을 달아준다
    - 다만 soft delete를 구현하려면 추가적인 처리가 필요하다
```java
@Entity
public class SampleEntity() {
	...
	
	@PreRemove
	private void preRemove() {
		// 수행 로직
	}
}
```
- @SQLDelete VS @PreRemove
    - @SQLDelete
        - remove() 메서드가 호출되면 실행된다
        - Delete 쿼리 대신 지정된 SQL 문이 실행된다
        - SQL을 직접 실행한다
        - 구현이 간단하고 직관적이다
        - 하지만 데이터베이스에 종속적인 SQL문을 작성해야한다
    - @PreRemove
        - remove() 메서드가 호출되면 실행된다
        - 엔티티의 상태를 변경할 수 있지만, 기본적으로 Delete 쿼리가 실행된다
        - 코드 내에서 로직을 실행해서 좀 더 유연한 로직을 구현할 수 있다
        - Delete 쿼리가 실행되니까 soft delete를 구현하려면 추가 처리가 필요하다