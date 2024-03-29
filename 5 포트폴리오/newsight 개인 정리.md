newsight 는 뉴스기사를 분류 및 토픽 제공서비스입니다.

뉴스기사를 분류해줌으로써 뉴스기사로 정보를 얻는 사람들에게 시간절약과 정확한 정보들을 얻게 할 수 있습니다.

간단하게 처리단계는

1. 수집 (한국언론진흥재단 api 사용) 및 DB 에서 뉴스기사전처리

- 불용어처리 : 은, 는, 이, 기 와 같은 한글  삭제, 
- 유의어처리 : 검색어와 비슷한 내용은 하나로 통합한다. ex) 미래에셋대우, 미래에셋
- 명사 추출 : 내용상의 동일함을 찾기 위해서 명사만 추출하는 것이 효과적이라 생각함

2. 유효 문서 추출

- 전처리된 문서에서도 주어진 검색어와 관련없는 문서가 많이 있었다. 따라서 유효문서 추출 단계가 필요했다.
- 여기서 유효 문서란 , 
  1. 제목에 검색어가 들어감
  2. 본문에 검색어가 3번 이상 들어감 

3. 카테고리 할당

- 카테고리는 한국언론진흥재단에서 지정한 뉴스 통합 분류체계 값인 '정치','사회','경제' 등을 활용했다.

4. 토픽 할당

- 토픽은 카테고리 하위 개념을 말한다. ex) '문재인' 검색 시 '정치'라는 카테고리에 '남북 정상회담' , '법안 상정' 등을 토픽이라 칭함
- 여기서 토픽 분류 알고리즘은 세가지 조건을 만족해야 한다.
  1. 카테고리별 토픽 수를 모르기 때문에 군집 수를 지정할 수 없다.
  2. 또한 실시간 서비스를 위해 처리속도가 빨라야 함
  3. 검색어간 분류성능이 균일해야하기 때문에 하이퍼파라메타가 적어야한다.
- 이를 만족하는 알고리즘으로 밀도 기반 비지도  군집화 알고리즘 OPTICS 를 사용
- OPTICS의 장점
  1. 군집수를 정할 필요가 없다.
  2. 밀도가 다른 군집이 있을 때, 각 군집의 맞는 밀도를 고려할 수 있다.
- 일반적으로 OPTICS는 DBSCAN보다 느린것으로 알려져있는데, 처리속도는 느리지만 최소 군집수만 정해도 비지도 분류를 할 수 있다는 점에서 OPTICS를 채택해 사용

5. 각 토픽에서 대표문서 추출

- 길이가 짧을 수록 효율적으로 정보를 전달하는 문서라고 생각하여 토픽 내 대표기사는 토픽 키워드를 많이 포함하면서 길이가 짧은 문서로 정의
- 토픽 키워드란 토픽 내 말뭉치에서 가장 많이 출현한 단어 10개를 의미
- bm25 scoring 으로 대표기사를 추출한다.