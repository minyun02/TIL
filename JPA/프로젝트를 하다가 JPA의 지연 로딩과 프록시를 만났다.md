개인 프로젝트에서 엔티티 간의 관계를 다루면서 지연 로딩과 프록시를 조금 더 이해할 수 있는 순간을 경험했다.

프로젝트에는 Match 엔티티는 inviter라는 Team 엔티티와 관계를 맺고 있다. 이 관계는 지연 로딩으로 설정되어 있다. 매칭을 수락하는 로직이 있는데 여기서 매칭을 보내온 inviter의 현재 상태를 확인해야 했는데, 여기서 생각과 다른 상황이 발생했다.

```java
matchValidator.validateTeamStatus(match.getInviter().getStatus());
```

위 코드가 그 상황이다. 지연 로딩을 사용할 때 조회되지 않은 엔티티는 프록시 객체 상태라는 개념은 알고는 있었다. 그리고 해당 엔티티가 필요한 순간에 조회 쿼리가 나간다는 사실도 알고 있었다. 그렇지만 정확히 어떤 시점이 해당 엔티티가 필요한 시점인지는 잘 모르고 있었다. 처음에는 match.getInviter() 이 부분에서 inviter라는 Team 엔티티가 필요하니까 조회 쿼리가 나갈거라고 생각했다. 로그를 보니 match.getInviter().getStatus() 처럼 실제 Team 정보가 필요한 순간에 조회 쿼리가 발생했다.

왜 그럴까? getInviter()로 inviter라는 Team 엔티티를 가져오는 순간까지는 프록시 객체를 전달하기 때문이다. 이 프록시 객체에는 inviter의 아이디값만을 가지고 있는 빈 겁데기 객체다. 그렇기 때문에 상태값이 필요한 순간에는 더 이상 진짜인 척 할 수 없기 때문에 inviter를 조회해온다.

이걸 알고 나니까 한가지 생각이 들었다. getStatus()로 상태를 가져오려는 inviter 엔티티가 DB에서 삭제된 상태라면 무슨 일이 생길까? 엔티티가 존재하지 않아서 EntityNotFoundException가 발생한다. 이런 이유로 match.getInviter().getStatus()는 안전하지 않다고 판단했고 inviter의 아이디값으로 실제 inviter 엔티티를 조회해서 존재 여부를 확인하는 방식으로 변경했다.

위에서 말했듯 inviter 프록시 객체가 가지고 있는 아이디값을 사용해서 좀 더 안전하게 처리할 수 있었다.

```java
Team inviter = teamService.getTeam(match.getInviter().getTeamId());
```