# CopyOnWriteArrayList
- 스레드 세이프 한 ArrayList
- 읽기 작업이 많고 쓰기 작업이 적은 상황에서 사용하기 적합하다
- 쓰기 작업 시 새로운 복사본을 생성하여 동시 읽기 작업과의 충돌을 방지한다
- 대량의 쓰기 작업 시 성능에 영향이 생길 수 있다

# 어떻게 동시성을 해결할까?
- 불변성 사용
    - 쓰기 작업(추가, 삭제, 수정)이 발생하면 기존 배열의 복사본을 만든다
    - 이를 통해 읽기 작업은 항상 일관된 상태의 데이터를 참조할 수 있다
- 동기화된 쓰기
    - 쓰기 작업은 synchronized 블록으로 감싸져 있다
    - 동시에 하나의 스레드만이 쓰기 작업을 수행한다

# ArrayList는 동시성 문제가 발생하면 어떤 일이 생길까?
- 데이터 불일치
    - 한 스레드가 요소를 추가하는 동안 다른 스레드가 요소를 읽거나 삭제하려 하면 예상치 못한 결과가 나올 수 있다
- ArrayIndexOutOfBoundsException
    - 한 스레드가 요소 추가나 삭제로 ArrayList의 크기를 변경하는 동안 다른 스레드가 동일한 ArrayList의 요소에 접근하려고할 때 인덱스 초과 예외가 발생할 수 있다
- ConcurrentModificationException
    - 한 스레드가 ArrayList를 반복하는 동안 다른 스레드가 요소를 변경하면 반복자가 상태를 감지해서 해당 예외가 발생한다
- 데이터 손실
    - 여러 스레드가 동시에 요소를 추가할 때 특정 요소가 덮어씌워지거나 삭제될 수 있다
