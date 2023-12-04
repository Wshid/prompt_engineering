## [CH08_01] LLM 애플리케이션 아키텍처

### Emerging LLM App Stack
- https://a16z.com/emerging-architectures-for-llm-applications/
- ![image](https://github.com/Wshid/dl_ml/assets/10006290/bffa0b5f-1bcb-47b0-a1f8-054925f6b883)
- LLM을 만들기 위한 다양한 컴포넌트

#### 분야별 정리
- 대부분 ML 스택과 겹치긴 하나, LLM을 위해 추가된 부분은 다음과 같음
  - Embedding model
  - Vector database
  - Playground
  - Orchestration
  - Validation
    - Json formatting을 통해 **결과 제어**
    - 평가를 통해 결과가 잘 나오는지 확인
    - 현재는 많이 발전되지 않은 영역
    - Json Formatting 및 Spreadsheet로 평가하는 것이 현재로서는 제일 좋은 방향
  - LLM APIs(proprietry)

| Data pipelines | Embedding model | Vector database | Playground | Orchestration | APIs/plugins | LLM cache |
|----------------|-----------------|-----------------|------------|---------------|--------------|-----------|
| Databricks     | OpenAI          | Pinecone        | OpenAI     | Langchain     | Serp         | Redis     |
| Airflow        | Cohere          | Weaviate        | nat.dev    | LlamaIndex    | Wolfram      | SQLite    |
| Unstructured   | Hugging Face    | ChromaDB        | Humanloop  | ChatGPT       | Zapier       | GPTCache  |
|                |                 | pgvector        |            |               |              |           |

| Logging / LLMops | Validation         | App hosting | LLM APIs (proprietary) | LLM APIs (open) | Cloud providers | Opinionated clouds |
|------------------|--------------------|-------------|------------------------|-----------------|-----------------|--------------------|
| Weights & Biases | Guardrails         | Vercel      | OpenAI                 | Hugging Face    | AWS             | Databricks         |
| MLflow           | Rebuff             | Steamship   | Anthropic              | Replicate       | GCP             | Anyscale           |
| PromptLayer      | Microsoft Guidance | Streamlit   |                        |                 | Azure           | Mosaic             |
| Helicone         | LMQL               | Modal       |                        |                 | CoreWeave       | Modal              |
|                  |                    |             |                        |                 |                 | RunPod             | 


### System Architecture
- ![image](https://github.com/Wshid/dl_ml/assets/10006290/3c582f17-82e3-4c7b-a399-abb3ee66ac5a)
  - Prompt Engineering, Orchestration이 제일 중요
- Prompt Engineering을 잘하기 위해
  - Chaining 향상
  - 평가 향상
  - prompt program이 잘 작동하는지 로그 확인
- Vector DB, 검색엔진
- 기업 내/외부 API
  - Prompt로 만들어진 결과 활용
- 파인 튜닝
  - 비용 최적화
  - 사내정보 내부적 이용

#### Orchestration
- https://medium.com/@simon_attard/leveraging-large-language-models-in-your-software-applications-9ea520fb2f34
- ![image](https://github.com/Wshid/dl_ml/assets/10006290/6ed919e0-4c27-403c-b453-4b89b2b553c3)
- task planning, context data 주입, prompt를 구성하여 결과 도출
- **프롬프트 실행 계획(Task Planner w/ DB)**
  - 프롬프트 여러 가지를 조합
  - 사용자가 어떤 명령 요청시, 그 요청을 다양한 프롬프트로 쪼개는 방법 -> 더 복잡한 명령 수행
- **컨텍스트 정보 획득**
  - Context Data w/ Vector DB
  - Memory Store w/ Vector DB
    - 대화 히스토리, 대화 기록을 통해 맥락 유지
    - 사용자가 요청하는 명령에 대해 더 자세히 사용자에게 맞춤형 결과 제공
  - **프롬프트 구성 및 실행**
    - Evaluation Module w/ DB
    - Prompt Manager
    - Response Manager
    - LLM Provider를 통해 여러 모델에서 결과를 가져와 취합, 사용자에게 전달

#### Application Modules
- Orchestrator
- LLM Provider
- Task Planner
- Context Data Vector Store
- Memory Data Vector Store
- Prompt Manager
- Response Manager
- External Tool Provider
- Prompt & Response log
- Evaluation Module
- 대부분의 경우, 이 모든 모듈이 다 필요하지는 않음
  - **필요한 task에 따라, 적절히 컴포넌트를 선택, 전체 시스템 구성**

### Application Architecture
- 태스크 플래닝
- 컨텍스트 정보 획득
- 프롬프트 조합
- 프롬프트 실행(-> 태스크 플래닝)
  - 프롬프트 실행 이후 결과를 사용자에게 전달하거나
  - 이 정보를 토대로 다른 태스크에서 수행
- 액션 API 실행(<-> 태스크 플래닝)
