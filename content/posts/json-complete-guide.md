+++
title = "JSON 완벽 가이드 - 웹 개발 필수 데이터 포맷 정복하기"
date = 2025-07-11T09:00:00+09:00
draft = false
description = "JSON 기본부터 실무 활용"
tags = ["json", "javascript", "웹개발", "api", "프론트엔드", "백엔드", "데이터"]
categories = ["웹개발", "JavaScript"]
series = ["웹개발 기초"]
+++
# JSON 완벽 가이드 - 웹 개발의 핵심 데이터 포맷

웹 개발을 하다 보면 정말 자주 마주치는 게 JSON이다. API 통신할 때도, 설정 파일 만들 때도, 데이터 저장할 때도 JSON을 쓴다. 하지만 정작 JSON이 뭔지, 어떻게 제대로 활용하는지 모르는 경우가 많다.

오늘은 JSON의 기본부터 실제 활용까지 한번에 정리해보겠다.

## JSON이 뭔가?

**JSON(JavaScript Object Notation)**은 클라이언트와 서버 간 데이터 교환을 위한 텍스트 기반 포맷이다. 이름에 JavaScript가 들어가지만 대부분의 프로그래밍 언어에서 사용할 수 있다.

JSON의 장점은 명확하다:
- 사람이 읽기 쉽다
- XML보다 훨씬 간결하다
- 파싱 속도가 빠르다
- 객체와 배열을 중첩해서 복잡한 구조도 표현 가능하다

## JSON 기본 문법

JSON에서 지원하는 데이터 타입은 다음과 같다:

```json
{
  "string": "문자열",
  "number": 42,
  "boolean": true,
  "null": null,
  "array": [1, 2, 3, "문자열"],
  "object": {
    "key": "value"
  }
}
```

실제 사용 예시를 보면 이런 식이다:

```json
{
  "name": "김개발",
  "age": 28,
  "isDeveloper": true,
  "skills": ["JavaScript", "React", "Node.js"],
  "address": {
    "city": "서울",
    "district": "강남구"
  },
  "projects": [
    {
      "name": "포트폴리오 사이트",
      "tech": ["React", "TypeScript"],
      "completed": true
    }
  ]
}
```

## JavaScript에서 JSON 다루기

### JSON.stringify() - 객체를 JSON 문자열로

JavaScript 객체를 JSON 문자열로 바꿀 때 사용한다. 서버에 데이터 보낼 때 필수다.

```javascript
const user = {
  name: "김개발",
  age: 28,
  skills: ["JavaScript", "React"]
};

const jsonString = JSON.stringify(user);
console.log(jsonString);
// {"name":"김개발","age":28,"skills":["JavaScript","React"]}

// 서버로 데이터 전송
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(user)
});
```

특정 속성을 제외하거나 가독성을 높이려면 이렇게 쓴다:

```javascript
const complexObject = {
  name: "김개발",
  password: "secret123",  // 민감한 정보
  skills: ["JavaScript", "React"]
};

// password 제외
const safeJson = JSON.stringify(complexObject, (key, value) => {
  if (key === 'password') return undefined;
  return value;
});

// 가독성 좋게 출력
const prettyJson = JSON.stringify(complexObject, null, 2);
```

### JSON.parse() - JSON 문자열을 객체로

JSON 문자열을 JavaScript 객체로 변환할 때 사용한다. 서버에서 받은 데이터 처리할 때 필수다.

```javascript
const jsonString = '{"name":"김개발","age":28}';
const user = JSON.parse(jsonString);

console.log(user.name); // "김개발"

// API 응답 처리
fetch('/api/users/1')
  .then(response => response.json())  // JSON.parse()와 동일
  .then(user => {
    console.log(user.name);
  });
```

잘못된 JSON을 파싱하면 에러가 나므로 try-catch로 감싸는 게 좋다:

```javascript
function safeJsonParse(jsonString, defaultValue = null) {
  try {
    return JSON.parse(jsonString);
  } catch (error) {
    console.error('JSON 파싱 실패:', error.message);
    return defaultValue;
  }
}

const result = safeJsonParse('invalid json', {});
```

## 실제 활용 사례

### API 통신

```javascript
class UserAPI {
  async getUsers() {
    const response = await fetch('/api/users');
    return response.json();
  }

  async createUser(userData) {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(userData)
    });
    return response.json();
  }
}
```

### 로컬 스토리지 활용

```javascript
// 설정 저장
const userSettings = {
  theme: "dark",
  language: "ko",
  notifications: true
};

localStorage.setItem('userSettings', JSON.stringify(userSettings));

// 설정 불러오기
const savedSettings = JSON.parse(localStorage.getItem('userSettings') || '{}');
```

### 설정 파일

```json
{
  "database": {
    "host": "localhost",
    "port": 5432,
    "name": "myapp"
  },
  "server": {
    "port": 3000,
    "environment": "development"
  }
}
```

## JSON vs XML

| 특징      | JSON | XML  |
| --------- | ---- | ---- |
| 가독성    | 높음 | 보통 |
| 파일 크기 | 작음 | 큼   |
| 파싱 속도 | 빠름 | 느림 |
| 주석 지원 | 없음 | 있음 |

같은 데이터를 표현해도 JSON이 훨씬 간결하다:

**JSON:**
```json
{
  "users": [
    {"id": 1, "name": "김개발"}
  ]
}
```

**XML:**
```xml
<users>
  <user>
    <id>1</id>
    <name>김개발</name>
  </user>
</users>
```

## JSON 검증하기

복잡한 데이터는 검증 로직을 추가하는 게 좋다:

```javascript
function validateUser(user) {
  const errors = [];

  if (!user.name || typeof user.name !== 'string') {
    errors.push('name은 필수 문자열입니다.');
  }

  if (!user.email || !user.email.includes('@')) {
    errors.push('유효한 email이 필요합니다.');
  }

  return {
    isValid: errors.length === 0,
    errors
  };
}
```

## 성능 최적화 팁

큰 JSON 데이터를 다룰 때는 스트리밍을 고려해보자. 그리고 불필요한 공백을 제거하면 파일 크기를 줄일 수 있다:

```javascript
// 압축된 JSON (공백 없음)
const compactJson = JSON.stringify(data);

// 가독성 좋은 JSON (공백 있음)
const prettyJson = JSON.stringify(data, null, 2);
```

## 마무리

JSON은 웹 개발에서 빼놓을 수 없는 핵심 기술이다. API 통신부터 설정 파일, 데이터 저장까지 어디든 쓰인다. `JSON.stringify()`와 `JSON.parse()`만 제대로 알아도 대부분의 상황은 해결할 수 있다.

실제 프로젝트에서 JSON을 다룰 때는 에러 처리와 검증을 잊지 말자. 그리고 민감한 정보는 JSON에 포함시키지 않도록 주의해야 한다.

JSON 관련해서 더 알고 싶다면 [JSON 공식 사이트](https://www.json.org/)나 [MDN 문서](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON)를 참고하면 된다.