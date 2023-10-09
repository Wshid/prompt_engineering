## [CH03_02] 프롬프트 엔지니어링 One Point Lesson

### One Point Lesson
- Prompt -> 생성 -> New Informations
  - **New Information은 잠꼬대에 가까움**
  - 희미한 기억에서 말을 꺼내오는 것과 같음
- 더 중요한 부분은 Context를 제공하는 것
- FLOW
  - Context in Human Language & Images -> 이해 -> Prompt -> 생성 -> New Information
  - 이후 이 New Information을 다시 Context로 돌려보내는 것
  - Prompt: 누구나 쉽게 실험하고 개발 가능
- Prompt는 그 자체로 프로그램이 될 수 있음

#### 예시
- 채용 공고 -> 이해 -> 면접 질문 만들어줘 -> 생성 -> 면접 질문 목록
  - 채용 공고를 어떻게 넣어주는가가 중요
- 채용 공고, 면접 질문, 면접 답변 -> 이해 -> 면접 답변 평가해줘 -> 생성 -> 면접 답변 평가
- 면접 답변 평가한 내용을 **심화 질문**생성 및 다음 턴 답변 고도화에 활용
  - 다시 면접 질문으로 넣음
- 어떤 정보를 이용하고 집어 넣을지, 어떻게 조합할지가 중요

#### LLM의 특징? 오류?
- "세종 대왕의 맥북프로 던짐 사건"
  - ChatGPT vs Bing Chat
- LLM은 틀리다는 말을 못함. 어떻게든 말을 만들어냄
- ChatGPT의 경우 임의의 말을 만들어냄
- 하지만 Bing Chat의 경우 올바르게 답변
  - 과정
    - "세종대왕의 맥북프로 던짐 사건"
    - Context From Web Search Result
    - 이해
    - Prompt
    - 생성
- **생성도 중요하지만 그 텍스트를 이해하고 텍스트를 생성하는가가 중요**

### ICL
- In-Context Learning
- 답변을 위해 필요한 **적절한 컨텍스트** 제공
- 원하는 결과 추출을 위한 **프롬프트** 작성
- 원하는 포맷의 출력을 위한 **프롬프트** 작성

#### 예시
- User Prompt
  - 유연 근무제가 있는 이커머스 플랫폼 개발 포지션 찾아줘
- Context from Vector Search
  - 내부 db에서 연관 정보를 모두 가져옴
  - AP의 Input으로 활용됨
- Assistant Prompt
  - 제공한 포지션 정보를 기반으로, 사용자가 요청한 내용에 대해 요약과 추천사를 작성해줘
- Personalized Answer Generation
  - **초개인화가 가능함**
