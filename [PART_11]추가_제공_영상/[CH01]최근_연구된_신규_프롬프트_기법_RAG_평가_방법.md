## [CH11_01] 최근 연구된 신규 프롬프트 기법 & RAG 평가 방법

### Generative AI, 제 2막
- 시장은 이제 Customer-back(고객 중심)으로 돌아섬
- 인간의 문제를 end-to-end
  - 솔루션 일부가 아닌
  - 워크플로우 전체를 수행하는 완전한 솔루션
- Generative AI 시장이 **기술 망치** -> **실제 사용 사례 및 가치 제공**으로 변화
- Generative AI App시장은 더 Multi-modal화 될 것

#### Enterprise뿐 아니라, 일반 사용자 시장까지 확대
- ![image](https://github.com/Wshid/dl_ml/assets/10006290/71aea994-8221-4a7e-8525-06467dae7ff4)
- 컴퓨터, 인터넷, 모바일 환경과 다르게, 몇 년이 아닌 몇 달만에 사용자에게 가치를 주는 제품으로 변화


### GPT-3.5 Find-tuning
- Fine-tuning Model의 가격
  - GPT-3.5-Trubo의 10배
  - GTP-4 * 1/2 ~ 1/3
- GPT-3.5 Find Tuning을로 GPT-4의 성능을 보일 수 있다고 함
- **비용을 절감하면서 낮은 지연 시간 요청이 가능함**
  - Fine-Tuning통해, 많은 예시를 제공하지 않아도 됨

#### 작업 방법
- 사용자 데이터를 모아 **GPT-4로 학습 데이터 생성**
- 해당 데이터로 `GPT-3.5-Turbo`를 파인 튜닝

#### 장단점
- 장점
  - 모델의 출력을 특정 Tone이나 Style로 조정하거나
  - 원하는 형식의 **구조화된 데이터**로 출력하는데 용이
- 단점
  - 학습을 위해 입력한 데이터를 정확히 출력 하지 못할 수 있음
  - 필요한 정보를 학습시켜도 Hallucination 발생 가능성 존재
- 따라서, 파인 튜닝 이후에 정확한 결과가 필요하다면
  - RAG를 통해 정확한 데이터 주입 필요

#### PlayGround에서 파인튜닝 하기
- 학습 데이터를 CSV로 업로드
- 학습 진행상황 모니터링 가능

#### 코드로 파인튜닝 하기
- colab 예시: https://colab.research.google.com/drive/1YGbCj_7MI1R7iulc01aPjj8zIyN9GSOr

### New Papers

#### 수능 국어 만점 프로젝트
- https://github.com/NomaDamas/KICE_slayer_AI_Korean

#### ChatGPT DALL-E 3 Prompt
- 영어로 아닌 언어로 지시하면, 영어로 번역한 뒤에 사용하라
- 100년이 지난 아티스트의 스타일은 만들수 없다고 말하라
- 다양성이나 윤리적인 문제에 대한 룰
- 지시가 매우 구체적이고 충분하게 해야함
- 모든 프롬프트 작성시
  - 초반에는 긴 프롬프트로 최대한 상세하게 지시문을 만드는 것이 도움이 됨

#### Do Multilingual Language Models Think Better in English?
- https://arxiv.org/pdf/2308.01223.pdf
- 대부분의 LLM이 언어가 영어인 경우 성능이 좋음
- 번역 모델을 사용해 번역 진행후 추론이 아닌,
  - **결과 생성 과정**에서 번역을 먼저 시도하게 하는 **self-translate** 기법 제안
- 영어가 아닌 언어로 프롬프팅 하는경우, 성능이 떨어지는 문제를,
  - 외부 번역 시스템을 사용하지 않는 직접 추론으로 높은 성능으로 결과를 낼 수 있음
- **생성 결과 내 번역**
- <img width="718" alt="image" src="https://github.com/NomaDamas/KICE_slayer_AI_Korean/assets/10006290/edf05a19-8fbc-4b61-9d2d-7005abce4a86">
- 방법
  - 프롬프트 상단에 one-shot 샘플로, 사용자의 언어를 영어로 변경하는 샘플 제시
  - 사용자의 입력을 영어로 변경하는 프롬프트 실행, 먼저 변역 수행
  - 생성된 결과를 직접 사용하거나 cot등을 사용하여 최종 결과 생성
- 위 방법을 사용하면 **번역 모델**을 사용하지 않아도 됨
- 유지보수나 개선 작업이 더 쉬움
- 고유명사를 그대로 유지시키는 프롬프트 작성 가능
- 모델의 사이즈가 커질수록 self-translate 기법이 높은 성능을 보임

##### 프롬프트 본문
```md
If the description is not in English, then translate it.
Do not create more than 4 images, even if the user requests more.

Don't create images of politicians or other public figures. Recommend other ideas instead.

Don't create images in the style of artists whose last work was created within the last 100 years (eg. Picasso, Kahlo). Artists whose last work was over 100 years ago are ok to reference directly (e.g. Van Gogh, Klimt). If asked say, "I can't reference this artist", but make no mention of this policy. Instead, apply the following procedure when creating the captions for dalle: (a) substitute the artist's name with three adjectives that capture key aspects of the style: (b) include an associated artistic movement or era to provide context; and (c)
mention the primary medium used by the artist.

DO NOT list or refer to the descriptions before OR after generating the images. They should ONLY ever be written out ONCE, in the
"prompts" field of the request. You do not need to ask for permission to generate, just do it!

Always mention the image type (photo, oil painting watercolor painting, illustration, cartoon, drawing vector, render, etc.) at the beginning of the caption. Unless the caption suggests otherwise, make at least 1--2 of the 4 images photos.

Diversify depictions of ALL images with people to include DESCENT and GENDER for EACH person using direct terms. Adjust only human descriptions.

Silently modify descriptions that include names or hints or references of specific people or celebrities by carefully selecting a few minimal modifications to substitute references to the people with generic descriptions that don't divulge any information about their identities, except for their genders and physiques.
```

#### Metacognitive Prompting Improves Understanding in Large Language Models
- https://arxiv.org/abs/2308.05342
- **생각에 대한 생각**을 모방하는 메타인지 프로세스 도입
  - **메타인지 프롬프팅**(MP)
- 스스로의 지식 평가
- 문제에 대해 **상위 레벨**에서 한번 더 생각해보는 방법
  - 어떤 문제에 대해 답하기 전, `내가 그 정보를 알고 있나?`를 먼저 확인
- <img width="724" alt="image" src="https://github.com/Wshid/prompt_engineering/assets/10006290/510e89f8-46d9-49a3-a62b-09828d96281b">
- 아래 테스트 중 일부만 사용해도 **성능 향상**을 꾀할 수 있음
  - 내가 이 정보를 알고 있나?
  - 내가 처음에 생각한게 맞나?
  - 내가 생각한 이 내용이 정확한가?
  - 내 결정이 맞나?
  - 내가 판단한 것이 맞나?
  - 이 판단한 내용이 신뢰도가 어느 정도인가?
  - 이런 것을 스스로 질문/답변하여 좀 더 **상위 개념**의 결과를 만들어냄 

##### 프롬프트 예시
- <img width="887" alt="image" src="https://github.com/Wshid/prompt_engineering/assets/10006290/535730ff-b4f0-4daa-9c0f-e90836e0ea75">
- 맨 위와 맨 아래에 있는 두 질문만 해도 일반적인 프롬프트보다 더 좋은 결과 도출 가능
  - `내가 질문한 정보를 내가 스스로 잘 알고 있는가?`
  - `그런 결론에 대해서 어느정도 자신감이 있는가?`
- 모든 과정을 다 거치기엔 성능/비용 문제 발생 가능성 존재

#### Take a Step Back: Evoking Reasoning via Abstraction in Large Language Models
- https://arxiv.org/abs/2310.06117
- LLM에게 구체적인 세부 사항을 포함하는 질문에서
  - **고수준 개념과 기본 원칙을 추출하는 추상화 수행**
- e.g. `Estella Leopold가 1954.8 ~ 1954.11 사이에 어느 학교를 다녔는가?` 라는 구체적 질문에서
  - `Estella Lepold의 교육 이력은 무엇이었나요?` 라는 덜 구체적인 높은 수준의 질문에 먼저 대답
- 메타인지 프롬프팅과 유사 맥락
- <img width="805" alt="image" src="https://github.com/Wshid/prompt_engineering/assets/10006290/c38aac80-839c-424e-9448-5a67502a000b">
- 프롬프트 안에서 직접적인 결과를 내는데도 유용하나,
  - **검색을 통한 RAG 기법을 사용할 때 더 강력한 효과를 낼 수 있음**
    - 더 상위의 질문이기 때문에 검색에 적합하기 때문

#### Autonomous Visual Information Seeking with Large Language Model Agent
-https://arxiv.org/abs//2306.08129
- LLM을 활용하여 외부 도구의 활용 전략을 **동적**으로 구성하고, 그 결과 조사
  - 제시된 질문에 대한 답변을 제공하기 위해 필요한 **필수 지식**을 획득하는 방법
- e.g. `이 이미지에 표시된 건물로 기리는 행사는 무엇입니까`
  - 외부 지식이 필요한 시각적 질문에 응답하는 복잡한 작업 해결 가능
- 여러 Tree-Search 방법에 가까움
- 예시
  - <img width="526" alt="image" src="https://github.com/Wshid/prompt_engineering/assets/10006290/97f158e6-0804-45d3-a627-e307a855b3a4">
- **LLM의 가장 중요한 활용 = 도구의 활용**
  - 가장 적법한 예시
- 앞으로의 AI 방향은
  - 스스로 도구를 사용하여 정보 습득
  - 도구를 직접 만들어 문제를 해결하는 방향 

#### Large Language Models Sensitivity to The Order of Options in Multiple-Choice Questions
- https://arxiv.org/abs/2308.11483
- LLM이 MCQ(다중 선택 문제)에서 선택지의 순서에 민감하게 반응
- `Lost in the Middle` 논문과 궤가 같음
- 예시
  - ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/82870225-79d3-43f3-912f-9dc4c42bfa56)
  - 이 예시는 `Lost in the Middle`이라기 보단
  - 일종의 COT 효과로, 앞뒤의 맥락때문에 오답 발생
- Context의 순서에 따라 영향을 받음
  - **Context 순서를 셔플하여 여러번 추론** 이후, Self-Consistency와 유사한 방법으로
    - **다수결에 따라 결과를 내면 됨**

#### Chain of Density Prompting
- https://arxiv.org/abs/2309.04269
- 요약문을 만들 때, 빠진 내용을 찾아 추가하고, 더 짧게 압축하는 작업을 5회 반복시
  - **아주 높은 품질의 요약문을 만들 수 있음**
- 예시
  - <img width="910" alt="image" src="https://github.com/Wshid/prompt_engineering/assets/10006290/31613967-fe15-437f-8aa1-f794332cfbb9">
  - 이전 요약을 했던 내용 중에서, 원래 문서에 없는 내용을 추출
  - 추출한 내용과 이전 요약문을 가지고, 새로운 요약문을 생성
    - 단, 전체 요약문의 길이는 더 길어지지 않게

#### Chain-of-Verification Reduces Hallucination in Large Language Models
- https://arxiv.org/abs/2309.11495
- LLM의 Hallucination을 줄이는 새로운 기법
- 모델 자체가 응답을 자동으로 검증하는 방법
- `기본 응답 생성, 검증 계획 수립, 검증 수행`의 과정을 거쳐 최종 응답 생성
- 본 강의 중 **autonomous agent**와 유사한 과정을 거침
- 프롬프트
  ```md
  1. 먼저 위의 질문에 대해 답변 초안을 작성해주세요
  2. 답변을 세부적으로 분해하여 여러 개의 작은 질문(검증 질문)을 생성해주세요
  3. 각 작은 질문에 대해 세부적으로 답변해주세요
  ```
- 예시
  - <img width="722" alt="image" src="https://github.com/Wshid/prompt_engineering/assets/10006290/29bc86d1-b765-41fd-b1e8-cba8e9803028">
- 이 방법에 추가적으로
  - 검증 질문에 대한 답을 **검색 엔진**이나 **Vector Search**를 통해 가져온 다음, 이를 통해 검증하면
    - Hallucination을 더 크게 줄일 수 있을 것으로 보임

#### Promptbreeder: Self-Referential Self-Improvement Via Prompt Evolution
- https://github.com/NomaDamas/KICE_slayer_AI_Korean
- 프롬프트 자동 최적화
- **프롬프트 엔지니어링의 마지막**
- 일종의 **유전 알고리즘**을 통해 task에 적용하도록 **프롬프트 진화**뿐 아니라, 어떻게 진화하는지도 개선
- 방법 숙달의 난이도가 높아, 쉽게 사용하기엔 어려움
- <img width="925" alt="image" src="https://github.com/Wshid/prompt_engineering/assets/10006290/9155eb8d-7145-4d50-a936-9340d1bbf849">
- 유전 알고리즘을 통해 2가지 개선
  - task에 대한 prompt
  - prompt를 개선하는 prompt
- 쉽게 사용할 수 있는 framework이 나오면 고려해볼만한 방법

### Collaborate AI
- 강사가 명명한 말
- 여러 AI가 협력하여 소프트웨어 개발, 토론을 통해 높은 품질의 답변 도출
- 여러 작은 task를 수행하는 agent가 많이 만들어지고, 이 에이전트가 협업하도록 만드는 **최상위 에이전트**가 만들어지면
  - 이는 **AGI**가 됨

#### Communicative Agents for Software Development
- https://arxiv.org/abs/2310.06117
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/af87db81-802f-49ab-85cf-2f358226e3a9)
- ChatDev Project
- 인간과 유사한 상태로 각 역할 부여 및 대화를 통해 고도화
- 설계, 코딩, 테스트, 문서화를 거쳐 소프트웨어를 만듦
- 테스트 중에 **역할 반전**을 수행
  - e.g. designer가 programmer가 되고, ...
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/188fc3a4-afa5-448d-957f-df7c1fa9fe51)
  - 어떤 언어로 개발할지
- 아직은 복잡한 프로그램은 작성하기 어려움

#### Meta Programming for Multi-Agent Collaborative Framework
- https://arxiv.org/abs/2308.00352
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/f2be3eb3-14bb-4353-8621-86ae973af4bc)
- ChatDev와 유사
- 전체 프로젝트를 한번에 개발 x
- 중간마다 사람의 피드백을 받아 개발하는 형태
- 더 디테일한 부분까지 처리 가능
- 일반적으로 회사에서 운영하는 하나의 프로젝트 팀 단위 레벨

#### Exploring Collaboration Mechanisms for LLM Agents: A Social Psychology View
- https://arxiv.org/abs/2310.02124
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/d89e1a3a-c8c1-4766-a2c5-e2c6f3ed4395)
- 다양한 LLM Agent와 협력해서, 문제해결을 할때는
  - 개별 Agent의 특성보다는 **협력하는 전략**이 중요하다는 연구 결과
- 소규모 Agent 집단에서는 서로 동조하는 경향이 강해짐
- 서로가 비슷한 결론에 도달하여 최적의 결과가 나지 않는 문제가 있었음
- 그에 따라, LLM Agent 집단에 **다양한 협력 전략 도입**을 제안함
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/c97122d7-24d3-42ac-9d0a-4b9fddf42268)
  - 각각의 사고 패턴이 다른 여러개의 Agent를 만듦
    - 그림상 1,2,3번의 Agent를 의미
  - 그 Agent를 다양한 방식으로 섞고, 그 그룹 안에서 논의하게끔 함
  - 논의 과정 이후, 최종적으로 하나의 결론을 도출함
  - 이 과정을 여러번 반복해서 답변 성능을 높힘
- **Agent 수, Dataset와 Round 수**가 성능에 상당한 영향을 미침
- 스스로 생각을 Reflection 하는 것 보다
  - 서로 Debate 하는 것이 오류가 적고 최적의 답변이 나왔다고 함
- 인간의 사회와 유사하다는 시사점이 나옴

#### Reconcile: Round-Table Conference Improves Reasoning via Consensus among Diverse LLMs
- https://arxiv.org/abs/2309.13007
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/3b1c918a-6642-4592-a68e-dedfc0ca959f)
- GPT-4, Bard, Claude2 와 같은 **대규모 언어 모델**이 원탁에 둘러 앉아 토론하면
  - **더 정확한 답변을 얻을 수 있음**
- 각각의 모델의 장점을 활용하고, 약점을 보완해서
  - 전체적인 추론 능력을 향상 시키는 방법
- 앞서 나온 협력과 유사하나
  - 각기 다른 회사의 LLM의 성향/능력이 다르기 때문에 더 좋은 결과를 도출할 수 있었음
- 장점
  - 여러 LLM을 쉽게 연결할 수 있음
    - 하나의 모델에 여러 값을 주입하는 형태가 아닌
    - 각 모델이 다른점을 그대로 활용하기 때문
- 단일 Agent를 사용하거나 **하나의 LLM**으로 다중 Agent를 만들었던 것의 baseline을 7.7%나 초과함
- **그냥 단순히 API만 호출하여 사용할 수 있는 방법이므로**
  - **매우 편리하고 유용한 방법**
- 앞으로 조금 복잡한 것을 하게 되면, 우선적으로 고려해보면 좋은 방법

### RAG
- 긴 컨텍스트 모델을 사용하는 것보다
  - RAG를 잘 활용하는 것이 **비용 최적화** 및 **성능**을 높일 수 있음
- RAG에서 중요한 것은 **임베딩 모델**의 선택과 **청킹 전략**임
- 여러가지 임베딩 모델을 **복합적**으로 사용하거나
  - **키워드** 및 **디비 쿼리**를 통해 복합적으로 정보를 가져오는 **하이브리드 검색**을 잘 사용해야 함

#### Kaggle - LLM Science Exam
- https://www.kaggle.com/competitions/kaggle-llm-science-exam/discussion/446422
- Kaggle 우승한 이후, 해당 방법이 공개됨
  - 참고할 만한 자료
- RAG에 사용할 데이터를 잘 정제하여 우승한 사례
- 다른 팀이 모델 개발에 집중할 때
  - **데이터 클렌징**과 적절한 **청킹 전략**을 실험함
- 데이터를 많이 모으는 것보다
  - **질이 높은 소수의 데이터**를 독보적으로 가지는게 더 좋음

#### 청킹 전략
- 가장 효율적인 청킹은 **문서의 종류**와 **task**에 따라 다르므로,
  - 다양한 테스트를 통해 처리할 문서와 task에 적합한 청킹 전략을 찾는것이 필요
  - silver bullet은 없음(은총알)
- 기반 가이드
  - **200~300개 정도의 토큰 단위로 청킹**을 하는것이 적당해보임
    - 물론 그 수는 계속 조절해 가야함
  - 처음엔 **문장/문단 단위**로 시도하며, 이로 어려울 경우 200~300개 정도 수준으로 청킹 이후 테스트
- 튜닝 시
  - 다소 틀려도 괜찮은지, 무조건 틀리면 안되는지에 따라 어떤 방식을 선택할지 기준이 되기도 함
- **검색에 최적화된 청킹 전략**과 **프롬프트에 넣어줄 생성 최적화된 청킹 전략**은 **다를 수 있음**
  - 각각 전략을 따로 세우고 **조합**하는 방법 고려 필요
  - 정보 검색시의 vector search를 높일 수 있는 chunk size와
  - 결과 생성시 **높은 성능**으로 결과를 생성하기 위해 LLM에 주입해야 될 정보에 chunk size가 다르다는 의미
- 일반적으로 Vector Search의 chunk size는 **한 문장/한 문단 단위 이상은 좋지 x**
  - 단, 앞뒤 맥락이 잘리게 됨
- 그에 따라 **생성을 위한 정보**는 더 큰 윈도우로 가져와서 제공할 필요가 있음

##### 예시
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/53e157c8-215f-4b01-b17b-39cde17b28fe)
- Vector Search를 위해 chunk는 **빨간색처럼 문장 단위**로 추출하여 chunk 위주 임베딩
  - 녹색1, 빨간색, 녹색2로 분리
- 하지만 생성을 할 때는, 이 청크의 앞뒤 몇개를 가져와서
  - 이를 가지고 rerank | 정보 판단 | 프롬프트 정보 주입 하는 방법 사용
  - 단, 이경우에도 주입할 컨텍스트 길이가 너무 커질 수 있으므로
    - 이 길이를 적절하게 유지할 수 있도록 연구 필요

#### Parent Document Retrieval
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/806fadcb-ac4a-45f0-9ddc-31264d5e2a3d)
- 청킹 전략의 확장
- Split Parent Chunk 부분처럼 chunk를 큰 단위로 분리
  - 한 페이지/문단 단위
- chunk를 다시 `한 페이지 -> 문단 | 문단 -> 문장` 으로 분리
- 이 **하위 chunk**를 Vector Search할 수 있도록 저장
- 검색시,
  - 하위 chunk를 검색
  - 검색 결과에서 텍스트를 가져올 때는
    - `하위 chunk + parent chunk`를 프롬프트에 주입
- 장점
  - 작은 문장/문단으로 **유사도 검색**이 되므로, **검색 정확도**를 높힐 수 있음
  - 주입하는 텍스트는 **부모의 텍스트**이므로, 윈도우가 열려 있어 **텍스트 맥락**은 유지할 수 있음
- chunk를 하위 청크로 나누는 방법을 확장하여
  - **트리 검색** | **그래프 검색**을 활용하여 더 높은 성능을 꾀할 수 있음

#### Pre-Generative Retrieval
- 강사가 이름을 붙임
- **문서의 일부분**에 대한 질문 생성, 해당 질문을 **임베딩**하여 검색
- 사용자의 요청이 **대부분 질문**으로 들어올 것을 예상하였을 때, 더 큰 성능 발휘
- 사용자의 질문에 대한 **여러개의 유사 질문**을 생성하고
  - 해당 질문과 원래 질문의 문서 검색 후 **가장 적합한 문서**를 생성에 사용
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/18a3c311-4768-4535-aa03-0a5016cc1289)
  - User Query(사용자 질문)
  - 유사 질문을 여러개 생성
  - 유사 질문 Vector Search + 원래 질문 Vector Search
    - 원래 질문을 다양하게 표현한 효과
    - 같은 문서여도, 다양한 순서로 검색 | 빠진 문서 같이 검색 가능
    - 빠진 문서 보완하면서, 상위로 랭크가 많이 올라온 문서들이 높은 우선순위를 가질 수 있음
    - **rerank도 쉽게 처리 가능**

### References
- Less is More: Why Use Retrieval Instead of Larger Context Windows (https://www.pinecone.io/blog/why-use-retrieval-instead-of-larger-context/)
- LLM을 사용하여 FAQ 검색 평가 데이터 세트 만들기(일본어)(https://www.aishift.co.jp/techblog/3710)
- Is ChatGPT Good at Search? Investigating Large Language Models as Re-Ranking Agent (https://arxiv.org/abs/2304.09542)
- RAG-Fusion: The Next Frontier of Search Technology (https://github.com/Raudaschl/rag-fusion)
- RAG and Parent Document Retrievers: Making Sense of Complex Contexts with Code (https://medium.com/ai-insights-cobet/rag-and-parent-document-retrievers-making-sense-of-complex-contexts-with-code-5bd5c3474a8a)
- Google Colab에서 JapaneseEmbeddingEval로 일본어 Embedding 평가 절차(일본어) (https://note.com/npaka/n/nce7b265b7133)
- Llamalndex의 성능 향상을 위한 기술 가이드(일본어) (https://note.com/npaka/n/n33e28a9e1402)
- Evaluating the Ideal Chunk Size for a RAG System using Llamandex (https://blog.llamaindex.ai/evaluating-the-ideal-chunk-size-for-a-rag-system-using-llamaindex-6207e5d3fec5)
- Optimize LLM Enterprise Applications through Embeddings and Chunking Strategy. (https://actalyst.medium.com/optimize-lIm-enterprise-applications-through-embeddings-and-chunking-strategy-1bbdb03bedae)

### 참고 도구
- Re-rank를 쉽게 적용할 수 있는 Cohere API
  - https://txt.cohere.com/rerank/
  - 기존에는 rerank를 하려면 **자체 모델**을 만들거나 **gpt4**등을 사용
    - 생성 속도가 느리거나 성능 부족
- 다양한 방식으로 청킹을 할 수 있는 도구
  - https://neumai-playground.streamlit.app/
  - 초기 프로토타이핑시 사용 가능
  - LangChain에 포함된 다양한 text-splitter를 사용하여 텍스트 분리
    - 여러가지 방법, 여러 개수 문자/토큰으로 잘라서 테스트 가능

### Evaluation
- LLM에 대한 평가 방식은 많이 연구되어 왔으나
  - RAG 결과나, pipeline, app 결과 평가 방식은 방법론이 x

#### Evaluation Criteria
- Faithfulness (신뢰성)
- Relevancy (관련성)
- Correctness (정확성)
- Guideline (지침)

#### LLM Evaluation
- 사용자의 요청을 텍스트로 생성, 결과를 내는 LLM 특성
- **기존의 테스트 방법들이 적절하지 x**
  - 사람이 정성적으로 테스트해야 하는 경우 존재
- GPT-4가 인간 수준의 판단력을 가지고 있다고 인정함
  - GPT-4에게 평가를 시키는 방법이 대세가 되어감
  - **LLM-as-a-judge**

#### Benchmark set w/ gpt-4
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/74119fa2-3795-4e13-9265-f6e842b28bad)
  - benchmark set을 위한 질문도 gpt4로 생성
  - 질문에 대한 답변도 gpt4로 생성
  - LLM이 생성한 결과도 gpt4로 평가
- 위 방법에 대한 평가가 부족하긴 하지만,
  - 유사한 평가를 가진다는 의견이 많아지고 있음

#### RAG Evaluation
- RAG 평가는 기존의 **자연어 평가**로 충분히 처리 가능
- 기존 방법인 **ROUGE**나 **BLEU**점수를 사용하여 평가 가능
- 일반적으로 평가 데이터를 만들 때
  - 질문에 따른 chunk를 미리 준비할 것이기 때문에
  - **유사도 판단**으로 평가하는것이 제일 적절함
- 평가 데이터를 미리 만들지 않은 경우 아래와 같은 일정 수준 이상의 성능 평가
  - **GPT-4로 질문/답변을 미리 생성하고**
  - **해당 답변과의 유사도로 평가하는 방법**
- 평가 만드는 방법 조차도 2주 ~ 1달 정도 걸릴 수 있음
- LLM을 만드는 과정의 비용
  - prototype 1md
  - app에 1mw, 2mw
  - 평가 2mw, 1mm

##### FLOW
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/893728fb-6956-42f9-b28b-c4a1efa3642d)
  - 원본 문서에서 GPT-4를 이용해 질문 세트 생성
  - 질문 세트에 있는 질문을 하나씩 가져오서 원본 문서를 가지고 답변 생성
- ![image](https://github.com/Wshid/prompt_engineering/assets/10006290/5398c8ed-3eb4-486f-a4da-6d43994a9296)
  - 질문 세트, 답변 세트를 GPT-4로 이용하여 만든 이후 chunk Test
  - chunk 방식도 여러가지 방법으로 테스트 진행
    - chunk를 문장 단위, 200token, 100token 등 다양하게
  - 질문 세트를 이용하여 chunk 검색
  - chunk로 가져온 결과 및 GPT-4로 만든 답변의 유사도 확인
- **chunk를 검색해오는 성능** 및 **답변을 생성해주기 위해 주입하는 chunk**의 품질 동시 평가 가능
- 물론, 사용자가 입력한 답변 세트를 **수작업**으로 만드는게
  - 가장 높은 품질의 결과를 얻을 수 있음
- 단, 처음 시도하거나 답변 세트를 수작업으로 만들기 부담스러울 경우
  - 일정 수준의 성능을 담보할 수 있음

#### Reference
- 청크 사이즈 및 Retrieval 방법을 테스트 해 볼 수 있는 Colab 노트
  - https://colab.research.google.com/drive/1Siufl13rLI-kII1liaNfvf-NniBdwUpS
- 아래 3개 툴은 유용하긴 하나, 편하게 쓸 수 있는 정도는 아님. 공부를 하거나 다른 방식으로 사용 필요
  - Ragas - RAG 파이프라인을 쉽게 평가 할 수 있는 프레임워크
    - https://docs.ragas.io
  - DeepEval - 다양한 방식으로 LLM 애플리케이션을 평가 할 수 있는 프레임웍
    - https://docs.confident-ai.com
  - LLM evaluation harness - LLM 모델 평가를 위한 라이브러리
    - https://github.com/EleutherAI/lm-evaluation-harness
