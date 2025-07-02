+++
title = "디바운스(Debounce) 완벽 가이드 - 입력 이벤트 최적화의 핵심"
date = 2024-12-18T00:00:00+09:00
draft = true
description = "디바운스의 개념, 동작 원리, 실전 예제와 활용 팁까지, 프론트엔드 개발에서 꼭 알아야 할 디바운스 패턴을 정리합니다."
tags = ["javascript", "디바운스", "이벤트", "최적화", "프론트엔드"]
categories = ["개발"]
+++

# 디바운스(Debounce) 완벽 가이드 - 입력 이벤트 최적화의 핵심

안녕하세요! 오늘은 프론트엔드 개발에서 자주 쓰이는 **디바운스(Debounce)** 패턴에 대해 알아보겠습니다.

## 디바운스란?

디바운스는 짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화하여, **일정 시간이 지난 후 단 한 번만 이벤트 핸들러가 실행**되도록 만드는 기법입니다.

### 언제 사용할까?
- 검색창 입력 시 Ajax 요청 최적화
- 윈도우 리사이즈, 스크롤 이벤트 최적화
- 자동 저장, 자동 완성 등 빈번한 입력 이벤트 처리

## 동작 원리

1. 이벤트가 발생할 때마다 타이머를 새로 설정합니다.
2. 이전에 설정된 타이머가 있다면 취소합니다.
3. 마지막 이벤트 발생 후 지정한 시간(delay) 동안 추가 이벤트가 없으면 콜백이 실행됩니다.

## 실전 예제: 입력창 자동완성 최적화

```html
<input type="text" id="search" placeholder="검색어를 입력하세요" />
<div id="result"></div>
<script>
  const input = document.getElementById('search');
  const result = document.getElementById('result');

  // 디바운스 함수 구현
  function debounce(callback, delay) {
    let timerId;
    return function (...args) {
      if (timerId) clearTimeout(timerId);
      timerId = setTimeout(() => {
        callback.apply(this, args);
      }, delay);
    };
  }

  // Ajax 요청을 흉내내는 함수
  function fakeAjax(query) {
    result.textContent = `검색 결과: ${query}`;
  }

  // 디바운스 적용
  input.addEventListener('input', debounce((e) => {
    fakeAjax(e.target.value);
  }, 300));
</script>
```

**설명:**
- 사용자가 입력할 때마다 300ms 동안 추가 입력이 없으면 fakeAjax가 실행됩니다.
- 입력이 계속되면 이전 타이머가 취소되어 Ajax 요청이 남발되지 않습니다.

## 활용 팁
- 서버 부하를 줄이고, 불필요한 네트워크 요청을 방지할 수 있습니다.
- 입력 이벤트 외에도 resize, scroll 등 다양한 이벤트에 적용 가능합니다.
- React, Vue 등 프레임워크에서도 동일하게 활용할 수 있습니다.

## 마치며

디바운스는 프론트엔드 성능 최적화의 필수 도구입니다. 다음 포스팅에서는 **쓰로틀(throttle)**에 대해 알아보고, 두 기법의 차이와 실전 활용법도 비교해보겠습니다!

---

**참고**: 이 포스팅의 예제 코드는 브라우저 콘솔에서 직접 테스트해볼 수 있습니다. 궁금한 점은 댓글로 남겨주세요! 😊