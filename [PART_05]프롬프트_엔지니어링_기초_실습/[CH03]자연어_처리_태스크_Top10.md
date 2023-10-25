## [CH05_03] 자연어 처리 태스크 Top 10

### Task 10

#### Text Generation
- Language Translation
  - 한 언어에서 다른 언어로 텍스트를 번역
- Style Translation
  - 격식 -> 비격식 전환
  - 텍스트 스타일을 변경
- Editing and Rewriting
  - 텍스트의 내용을 명확하게 하거나, 의도를 더 잘 전달하도록
  - 문장 또는 문서를 수정하거나 다시 작성하는 작업
- Summarization
  - 긴 텍스트를 핵심 내용을 포함하는 짧은 텍스트로 축소하는 작업

#### Text Analysis
- Named Entity Recognition
  - 텍스트에서 특정 정보(사람, 장소, 조직, 날짜 등)를 식별하는 작업
- Sentiment Recognition
  - 긍정적, 부정적, 중립적 등 텍승트의 다양한 감정 상태를 판단하는 작업
- Document Classification
  - 텍스트 문서를 사전 정의된 카테고리 또는 클래스로 분류하는 작업
  - e.g. 뉴스 기사를 정치, 경제, 스포츠 등의 카테고리 분류
- Topic Modeling
  - 문서 집합에서 주제를 발견하고 각 문서에 주제를 할당하는 작업
- Similarity Evaluation
  - 두 개의 텍스트 사이의 유사성을 측정하는 작업
- Question Answering
  - 주어진 텍스트에서 질문에 대한 정보를 찾아 답변하는 작업
  - e.g. 해리포터에 대한 위키피디아 문서 제공 후 `해리포터의 작가는 누구인가요?`

### 실습 - Playground
```md
# Language Translation
다음을 한국어로 바꿔줘

# Style Translation
다음 비격식 표현을 격식 표현으로 바꿔줘

"야 뭐하냐?"
- temperature: 2로 수정시 잘못된 답변 반환

# Editing and Rewriting
다음에서 이름이 있는 문장을 마스킹 해줘

마스킹 예) 김진중입니다. -> ㅇㅇㅇ 입니다

"안녕하세요, 저는 김진중 입니다"

# Summarization
다음을 세 문장으로 요약해줘. 문장마다 줄바꿈을 해 줘

---

{문장 제시}

# Named Entity Recognition
다음에서 회사명, 제품명, 날짜를 추출해줘

{문장 제시}

# Sentiment Recognition
다음 리뷰를 긍정, 부정, 중립으로 나눠줘

"이 강의는 정말 유익해요"
"음식이 정말 맛이 없어요"
"음식 맛이 그럭저럭 괜찮네요"
```
- 이 때 temperatture 1 -> 2로 수정시 잘못된 결과가 반환될 수도 있음
- temperature가 낮으면 고정된 답변을 제공(e.g. 0)
- maximum length가 짧으면, 답변이 잘릴 수 있음
