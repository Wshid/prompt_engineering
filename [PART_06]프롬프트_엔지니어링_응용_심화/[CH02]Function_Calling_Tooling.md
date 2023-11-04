## [CH06_02] Function Calling(Tooling)

### Function Calling
- GPT 모델에 호출할 수 있는 **함수**(기능)를 설정해두면,
  - 사용자의 요청이 해당 기능이 필요한 요청일 때, 해당 함수를 호출하는 기능
- 직접 함수를 호출하는 것은 아니고,
  - **어떤 함수와 파라미터가 필요한지를 JSON형식으로 응답**
- ChatGPT Plugin이 이 기능, Function Calling을 사용하여 만든 것
- 아직 PlayGround에서 테스트가 불가능 하며, 코드를 작성하여 실험 필요

### 함수 정의 예시 및 설명
```python
messages = [t"role": "user", "content": 서울 날씨는 어때?"}]
functions = [
  {
    "name": "get_current_weather",
    "description": "주어진 위치의 현재 날씨를 가져옵니다.",
    "parameters":
      "type": "object"
      "properties": {
        "location": {
          "type": "string",
          "description": "도시 또는 지역, 예) 서울",
        },
        "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]},
        "required": ["location"],
      },
  }
]

response = openai. ChatCompletion.create(
  model="gpt-3.5-turbo-0613", 
  messages=messages,
  functions=functions,
  function_call="auto",
)
response_message = response["choices"][0]["message"]
```
- `name`
  - 필요한 기능 확인시 반환할 함수명
  - 이 함수명을 돌려받아서 프로그램내에 동일한 이름의 함수를 호출하거나
    - 혹은 분기 처리시에 키로 사용 가능
- `description`
  - 해당 함수에 대한 설명
  - GPT가 이 설명을 참조하여, 이 설명에 부합하는 내용인지 판단한 다음
  - 부합한다면 이 함수명 반환
- `parameters`
  - 함수 호출에 필요한 파라미터 정의
  - 이 파라미터 구조를 참고하여, GPT가 해당 함수를 호출할때 필요한 파라미터를 생성하여 전달
- `properties`
  - 함수에 필요한 파라미터 정의
  - 파라미터의 타입과 설명이 들어감
- `required`
  - 함수를 호출하기 위한 필수 파라미터 정의
  - 사용자의 메세지에 `required`에 해당하는 내용이 없다면, 사용자에게 필요하다고 역질문

### 호출 예시
```yaml
# Input
서울 날씨 알려줘

# Response
{
  'role': 'assistant',
  'content': None,
  'function_call': {
    'name': 'get_current_weather',
    'arguments':
      '{"location": "Glasgow, Scotland", "format": "celsius"}'
  }
}
```
- GPT가 필요한 정보가 다 있음을 확인,
  - get_current_weather라는 함수를 호출해야 한다는 결과 반환
- properties의 정보를 참고하여 arguments와 파라미터를 생성하여 전달
- `arguments`는 json 객체가 아닌 문자열 형태
  - `arguments`값을 받아 json 형태로 파싱한 다음 활용 필요

### SQL 쿼리 작성 예시
- https://github.com/openai/openai-cookbook/blob/main/examples/How_to_call_functions_with_chat_models.ipynb
```python
    {
        "name": "ask_database",
        "description": "Use this function to answer user questions about music. Input should be a fully formed SQL query.",
        "parameters": {
            "type": "object",
            "properties": {
                "query": {
                    "type": "string",
                    "description": f"""
                            SQL query extracting info to answer the user's question.
                            SQL should be written using this database schema:
                            {database_schema_string}
                            The query should be returned in plain text, not in JSON.
                            """,
                }
            },
            "required": ["query"],
        },
    }
```
- `database_schema_string`에 데이터베이스 스키마 전체 내용 추가 필요

### SQL 쿼리 호출 예시
```yaml
# Input
Hi, who are the top 5 artists by number of tracks?

# Response
{
  'role': 'assistant',
  'content': None,
  'function_call': {
    'name": 'ask_database',
    'arguments': '{\n "query": "SELECT ar.Name, COUNT (t. TrackId)
AS NumTracks FROM Artist ar INNER JOIN Album al ON ar.ArtistId =
al.ArtistId INNER JOIN Track t ON al.AlbumId = t.AlbumId GROUP BY
ar.ArtistId ORDER BY NumTracks DESC LIMIT 5"\n}'
  }
}
```

### json 포맷팅
- http://medium.com/@joseph.r.martinez/return-json-from-gpt-65d40bfc2ef6
```python
{
  "name": "set_recipe",
  "parameters": {
    "type": "object",
    "properties": {
      "dish": {
        "type": "string",
        "description": "Descriptive title of the dish"
      },
      "ingredients": {
        "type": "array",
        "items": {"type": "string"}
      },
      "instructions": {
        "type": "array",
        "description": "Steps to prepare the recipe.",
        "items": {"type" : "string"}
      }
    }
}
```

### json 포맷팅 호출 예시
```yaml
# Input
버터 토스트 레시피를 알려줘

# Response
{
  "dish": "버터 토스트",
    "ingredients": [
      "빵 조각",
      "버터"
  ],
  "instructions": [
    "붙지 않는 프라이팬이나 철판을 중간 불로 가열합니다.",
    "각 빵 조각의 한쪽에 버터를 펴 바릅니다. ",
    "버터면이 아래로 향하도록 뜨거운 프라이팬이나 철판에 빵 조각을 놓습니다.",
    "약 2-3 분 동안 또는 바닥면이 노릇 노릇하고 바삭해질 때까지 요리합니다.",
    "빵 조각을 뒤집고 1-2 분 더 익힙니다.",
    "프라이팬이나 철판에서 꺼내서 즉시 제공합니다."
  ]
}
```

### Function Calling 사용시 유의사항

#### Prompt Injection
- Function calling 사용시 유의사항
- Prompt Injection을 통해 민감정보를 추출하거나
- RCE(Remote Code Execution) 공격을 사전에 고려하여 방어해야 함
- 사용자의 아이디나 비밀번호 등 민감정보가 필요한 함수인 경우
  - **파라미터 설정에 넣지 않도록 주의**
- 응답받은 함수를 바로 실행하지 말고,
  - 보안상 문제 없는지,
  - 정상적인 파라미터와 함께 호출하도록 응답되었는지를 확인한 후 실행

#### 호출 오류
- 모든 요구 사항에 항상 정확하게 응답하는 것이 아니므로
  - 원하지 않는 경우에도 함수 호출 가능성 존재
- GPT가 설정하지 않은 함수라도 설정 내용으로 추측해
  - 존재하지 않는 함수를 호출하는 경우 발생 가능
  - e.g. 함수 설정에 Get만 존재하나, Add, Delete 함수를 추측해 호출
- 요청사항을 잘못 해석해 파라미터를 잘못 설정, 응답하는 경우가 발생

#### 유의사항 요약
- 응답받은 파라미터 값을 **항상 확인하고 실행**
- 중요한 동작을 실행할때는, **사용자에게 실행할 액션을 보여주고 확인한 뒤 실행**
- 정밀도가 높아야 하는 경우 되도록 **gpt-4**를 사용
