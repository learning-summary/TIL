# DB 파티셔닝(Partitioning)

### 0. 들어가기
보통 `db 조회 성능을 높이려면 어떻게 해야할까요?` 라는 질문에 index를 통한 index scan으로 성능을 높이면 된다고 대답한다. 하지만 데이터가 많아질수록 index 또한 방대해지며, 이에 대한 수정 또한 어려워진다. 이럴때는 partitioning을 통해 조회하려는 table의 물리적인 크기를 줄이는 것이 우선적으로 고려되어야 한다.

### 1. 정의
- 하나의 table에 너무 많은 data가 들어가면 조회, 수정시 성능이 저하된다(index를 수정할 경우엔 더욱!)
- 이를 해결하기 위해 table의 data 또는 index를 partition으로 나누어 관리하는 기법을 Partitioning 기법이라 한다.
  - 일종의 DB에서 분산 처리라고 볼 수 있다.
- 성능 향상
  - ___조회 성능 향상___(특히, full scan 시, data access 범위 줄어들기에 효율적)
  - 대용량 write 성능 향상
    - OLTP 시스템에서 많은 insert가 발생할 시, 이를 작은 partition 단위로 쪼갬으로써 ___disk i/o를 분산하기에 경합을 줄일 수 있음___
- 가용성
  - 테이블 하나가 훼손되더라도, 해당 partition 외에는 영향받지 않음
  - 각 partition별로 ___독립적인 백업 및 복구___ 가능
- 단점
  - table간 join 비용 증가
  - table 분리시키면 index도 같이 partitioning 해야함

### 2. 종류
- horizontal partitioning

  ![image](https://github.com/user-attachments/assets/354a0420-5fd3-421f-acd5-482e1befffcf)
  - Sharding과 동일한 개념
  - shard key 기준으로 데이터 나눔. 즉, ___schema가 동일한 데이터를 여러 테이블로 나누어 저장___
    - 일반적인 분산 저장 기술은 이 horizontal, sharding을 의미함
    - DBA가 access pattern과 균등저장을 고려하여 shard key 결정
  - 예시
    - customerId를 shard key로 사용한다 가정하면, 0~10000번을 1번째 partition에, 10001~20000번을 2번째 partition에 저장할 수 있다.
- vertical partitioning

  ![image](https://github.com/user-attachments/assets/78a998b4-765b-432f-ae4c-6d27c800eb1b)
  - 특정 column 단위로 쪼개서 관리. 즉, RDB에서의 제3정규화와 유사. 물론 3NF는 다른 후보키 의존하지 않도록 분리하는 것이고, ___vertical partitioning은 이미 정규화된 테이블을 분리하는 것___
  - ___자주 사용하는 data와 덜 사용하는 data를 분리___ 하는 기법

- 예시

    ![image](https://github.com/user-attachments/assets/94ab1c8d-1e02-4084-9781-437af22656e6)
    - 로그인 시, Address, Preferences 등은 불필요한 데이터임. 또한 Preferences는 자주 변경되지만 UserName은 거의 변하지 않음. 이러한 불필요한 loading을 분리하기 위해 vertical partitioning 진행
    
    ![image](https://github.com/user-attachments/assets/fc51caed-15a5-4386-8f3e-0e3636ffac8e)
    - ___Read 집중적인 테이블과 Write 집중적인 테이블로 분리___ 가능

- 장/단점 비교

  |특징&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|___horizontal___|___vertical___|
  |--|--|--|
  |__장점__|데이터 개수가 작아지니 index도 작아져 성능 향상|자주/덜 사용하는 column 분리시켜 read 집중, write 집중 테이블 분리하여 성능 향상. 불필요한 data 없으니 i/o 효율적|
  |__단점__|서버간 연결 많아짐. 데이터 찾기 복잡함. 하나의 서버 고장나면 무결성 깨짐|join 필요하여 단일 테이블보다 조회 느릴 수 있음. 별도 index 필요. transaction 관리 복잡(ex. 회원가입 시, User와 UserPreferences 테이블 2개 관리해야함)|


### 참고
- https://gmlwjd9405.github.io/2018/09/24/db-partitioning.html
