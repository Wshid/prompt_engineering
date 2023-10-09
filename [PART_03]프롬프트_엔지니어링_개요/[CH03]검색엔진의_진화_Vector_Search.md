## [CH03_03] 검색엔진의 진화 Vector Search
- 사용자 요청에 대한 정보를 가져오는 방법?

### Embedding
- anantine amigos(Text) -> Embedding Model -> Text as Vector
  - $-0.0.27 -0.001 -0.0.20 \cdots -0.0.23$
- Embedding Model: BERT, GPT

### Embedding Vector
- 단어를 입력받으면, 실수의 벡터 형태를 리턴

### Embedding Space
- 여러 차원에 걸쳐서 이 단어가 어느 위치에 있는가
- 2차원, 3차원
- 유사한 의미를 가진 단어는 clsutering 될 것

### Vector Search
- `'Apple'과 유사한 단어를 찾아`
- 해당 단어를 기준으로 주변을 탐색하는 과정
- Distnace or Similarity
  - 각 요소간의 거리
  - `Apple과 Banana의 거리`
- 의미 기반의 검색(Semantic Search)
- e.g. Male-Female, Verb Tense, Country-Capital

### Sentense Embedding
- 문장을 가지고 벡터를 만들어 냄
- 문장도 Embedding Space를 만들 수 있음

### Vector Search -> Semantic Search
- 사용자의 쿼리에 대한 의미에 가장 가까운 단어를 찾음
- No more
  - Full Text Indexting
  - 형태소 분석
  - 유사어/다국어 처리

### 속도 문제
- 대용량 데이터를 탐색하는데 이슈가 있음

### 성능 문제
- ANN(Approximate Nearest Neighbor)를 사용
- **속도 문제**로 모든 벡터를 비교하지 않고, **근사값**을 찾는 방법
- 어느정도 성능 해결은 했으나, 그만큼 정확도나 속도에 대한 부분이 있음
- Trade-off

### Hybrid Search
- 위 두가지 문제를 절충하기 위한 방법
- keyword 검색을 통해 DB에서 후보 데이터를 가져옴
  - 후보들이 어떤 id를 가지는지 확인
- 데이터에서 사용자가 요청한 내용의 유사 id
- 두가지 id를 혼합한 방식으로 활용

### Prompt Engineering
- In-Context Learning
- Vector search DB를 만드는 회사가 각광받고 있음