### JSON 이 뭔가요?

JSON은 JavaScript Object Notation의 약자입니다.

JSON은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷입니다.

자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷으로, 대부분의 프로그래밍 언어에서 사용할 수 있습니다.

객체 리터럴과 유사하게 키와 값으로 구성된 순수한 텍스트입니다

```
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```

### JSON이 제공하는 정적 프로토타입 메서드에 대해 몇가지 말해볼 수 있나요?

1. JSON.stringify()
- JSON.stringify 메서드는 ① 객체를 ② JSON 포맷의 문자열로 변환한다
- 클라이언트가 서버로 객체를 전송하려면 객체를 문자열화해야 하는데 이를 직렬화(serializing)라 한다
1. JSON.parse()
- JSON.parse 메서드는 ① JSON 포맷의 문자열을 ② 객체로 변환한다
- 서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열이다
- 이 문자열을 객체로 사용하려면 JSON 포맷의 문자열을 객체화해야 하는데 이를 역직렬화(deserializing)라 한다