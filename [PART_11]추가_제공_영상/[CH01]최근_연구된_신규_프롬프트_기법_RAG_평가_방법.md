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

### RAG

### Evaluation