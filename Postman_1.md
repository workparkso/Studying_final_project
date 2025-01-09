## Postman 소개 및 사용 방법

Postman은 API 개발, 테스트 및 문서화를 지원하는 **API 클라이언트**이다.  
REST, SOAP, GraphQL API를 효율적으로 요청하고 응답을 확인할 수 있도록 도와준다.  
백엔드 개발, 프론트엔드 통합, QA 테스트 등 다양한 분야에서 활용된다.

## Postman의 주요 기능

1. **API 요청 전송**  
   - GET, POST, PUT, DELETE 등 다양한 HTTP 요청을 쉽게 생성하고 실행 가능하다다.

2. **컬렉션 관리**
    - API 요청들을 그룹화하여 '컬렉션'으로 관리할 수 있다.
    - 다양한 환경 설정을 통해 개발, 스테이징, 프로덕션 환경을 손쉽게 전환할 수 있다.

2. **API 모니터링 및 협업** 
   - API 상태를 실시간으로 모니터링 등, 팀원과 공유 가능하다.

3. **환경 변수 사용**  
   - 환경별로 URL, 인증 토큰 등을 설정하여 API 테스트를 효율적으로 수행 가능하다.

4. **자동화 테스트**  
   - JavaScript 기반 스크립트를 작성해 API 테스트 자동화 가능하다.

5. **API 문서화**  
   - API 문서를 자동 생성하고 공유할 수 있다.


---

## Postman 사용 방법

### 1. Postman 설치 및 설정
- Postman은 [공식 웹사이트](https://www.postman.com/downloads/)에서 다운로드할 수 있으며, Windows, macOS, Linux에서 사용할 수 있다.
- 설치 후 Postman을 실행하고, 계정을 생성하거나 로그인하여 팀과 협업할 수 있다.

---

### 2. API 요청 보내기
1. Postman을 열고, 왼쪽 상단에서 **New** 버튼을 클릭한 후 **Request**를 선택한다.
2. **HTTP 메서드**를 선택하고, 요청할 **URL**을 입력한다.
3. 요청의 **Headers**나 **Body**를 설정할 수 있다. 
   - 예를 들어, POST 요청의 경우 JSON 데이터를 바디에 추가할 수 있다.
4. **Send** 버튼을 눌러 요청을 보낸다.

---

### 3. 응답 분석하기
- 요청 후 받은 **응답(Response)**을 확인할 수 있다.
- 응답은 JSON, HTML, XML 등의 형식으로 표시된된다.
- 상태 코드, 응답 시간, 크기 등을 확인하고, 응답 본문을 JSON 형식으로 분석할 수 있다.

---

### 4. 컬렉션 사용하기
- 여러 API 요청을 하나의 **컬렉션(Collection)**으로 그룹화하여 저장할 수 있다.
- 컬렉션은 API 테스트 스위트처럼 동작하며, 요청을 재사용하거나 공유하는 데 유용하다.


---


## JSON 예시: POST 요청

다음은 JSON 형식으로 요청 본문을 작성하는 예시입니다. 이 예시는 사용자 정보를 API에 POST 요청으로 보내는 경우입니다.

---

### 1. 요청 설정

- **HTTP 메서드:** POST  
- **URL:** `https://api.example.com/users`  
- **Headers:**  
  - `Content-Type: application/json`  
- **Body (raw, JSON 형식):**

```
{
  "name": "WorkParkso",
  "email": "workPakrso@example.com",
  "age": 26
}
```

- 응답예시

```
{
  "status": "success",
  "data": {
    "id": 12345,
    "name": "WorkParks",
    "email": "workparkso@example.com",
    "age": 30
  }
}
```

이 요청은 https://api.example.com/users에 새로운 사용자를 추가하는 API 요청을 보낸다. 요청 본문에 사용자 정보가 JSON 형식으로 포함되어 있으며, 서버는 새 사용자 정보를 포함한 응답을 반환하는 것의 흐름을 보여준다.


---

### Python을 사용한 Postman API 호출 예시
물론 Postman을 사용하지 않고 Python에서 API 요청을 보낼 때는 `requests` 라이브러리를 사용할 수 있다.
postman 사용에 직접적인 내용이 아니라서, 마지막에 추가적인 내용으로 작성하게 되었다.

```
import requests
import json

url = "https://api.example.com/users"
headers = {
    "Content-Type": "application/json"
}
data = {
    "name": "John Doe",
    "email": "johndoe@example.com",
    "age": 30
}

response = requests.post(url, headers=headers, json=data)

# 응답 결과 확인
if response.status_code == 200:
    print("User created successfully:")
    print(response.json())
else:
    print(f"Failed to create user: {response.status_code}")
```
이 코드는 Python을 사용하여 JSON 형식의 데이터를 POST 요청으로 API에 보내는 예시이다. 응답을 JSON 형식으로 받아 출력하게된다.


---
Postman은 API 개발 및 테스트에 필수적인 도구로, 사용자 인터페이스가 직관적이고 강력한 기능을 제공하는 것을 알 수 있다. JSON 형식의 데이터를 사용한 API 요청과 응답을 쉽게 처리할 수 있으며, Python을 사용하여 Postman과 같은 기능을 구현할 수도 있다는 것을 알게 되었다. 최종 프로젝트를 하면서 Postman을 사용할 수 있는 기회가 많아져서 보다 많은 기능들을 알아가는 시간이 되었다. 알게된 하나의 기능인, API 문서화를 통해서 HTTP 메서드를 깔끔하게 정리해서 프로젝트에 깔끔히 정리된 시각적인 효과를 기대할 수 있게되어 좋았다.