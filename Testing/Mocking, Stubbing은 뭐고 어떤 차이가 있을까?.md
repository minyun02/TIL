# Mocking
- 식제 객체의 행동을 모방하는 가짜 객체를 만드는 과정이다
- 객체의 동작을 검증할 대 사용한다
- 행동 기반 테스트에 사용된다
# Stubbing
- 특정 메서드 호출에 대해 미리 정의된 값을 반환하도록 설정하는 과정이다
- 상태 기반 테스트에 사용된다

# 사용 예시

```groovy
def "검색 조건이 있는 경우"() {
    given:
    def matchRepository = Mock(MatchRepository) // Mocking
    def matchService = new MatchService(matchRepository)
    def teamId = 1L
    def matchSearchDTO = new MatchSearchDTO()

    and: // Stubbing
    matchRepository.findAllMatchesByInviteeId(teamId, _, _) >> [new Match(), new Match(), new Match(), new Match()]

    when:
    def result = matchService.getReceivedMatchList(teamId, matchSearchDTO)

    then:
    result.size() == 4 // Stubbing?
    1 * matchRepository.findAllMatchesByInviteeId(teamId, _, _)
}
```
- 개인적으로 진행 중인 프로젝트의 테스트 코드를 참고해서 만들어보았다
- 이 테스트 코드는 spock이라는 테스트 프레임워크를 가지고 만들었기 때문에 groovy를 사용했다
- given 블록에서는 테스트에 필요한 재료들을 만들어준다
- 해당 로직에서는 매치를 DB에서 조회해야하기 때문에 MatchRepository가 필요하다
- def matchRepository = Mock(MatchRepository) 라인에서 가짜 MatchRepository 객체를 만들어준다
- 이 작업이 Mocking이다
- 그리고 이제 MatchRepository의 매치 조회 메서드가 호출됐을 때 반환값을 정의해줘야 한다
- and절에서 이 작업이 이루어지고 이걸 Stubbing이라고 부른다
- 그러면 then 절에서 result 크기가 4인지 비교하는건 Stubbing일까?
- 처음 Stubbing 개념을 잘 이해하지 못했을 때는 4라는 값을 정의한거니까 이 부분도 Stubbing이라고 생각했다
- 하지만 찾아보니까 result의 크기를 비교하는 부분은 설정한 결과를 검증하는 assertion에 해당한다