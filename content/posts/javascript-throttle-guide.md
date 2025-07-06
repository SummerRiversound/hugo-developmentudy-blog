+++
title = "쓰로틀(Throttle) 완벽 가이드 - 스크롤/이벤트 최적화의 핵심"
date = 2025-07-06T00:00:00+09:00
draft = false
description = "쓰로틀의 개념, 동작 원리, 활용 팁까지, 프론트엔드 개발에서 꼭 알아야 할 쓰로틀 패턴."
tags = ["javascript", "쓰로틀", "이벤트", "최적화", "프론트엔드"]
categories = ["개발"]
+++

# 쓰로틀(Throttle) 완벽 가이드 - 스크롤/이벤트 최적화의 핵심

안녕하세요! 개발에서 자주 쓰이는 **쓰로틀(Throttle)** 패턴에 대해 알아보겠습니다.

## 쓰로틀이란?

쓰로틀은 짧은 시간 간격으로 연속해서 발생하는 이벤트를 **일정 시간마다 한 번씩만 실행**되도록 제한하는 기법입니다.

### 언제 사용할까?
- 스크롤 이벤트 최적화 (무한 스크롤, 고정 헤더 등)
- 윈도우 리사이즈 이벤트 최적화
- 마우스 이동, 드래그 등 고빈도 이벤트 처리

## 동작 원리

1. 이벤트가 발생하면, 지정한 시간(delay) 동안 추가 이벤트를 무시합니다.
2. delay가 지난 후 다시 이벤트가 발생하면 콜백이 실행됩니다.
3. 이 과정을 반복하여, **최대 1초에 한 번** 등으로 호출 빈도를 제한할 수 있습니다.

## 실전 예제: 스크롤 이벤트 최적화

```html
<div class="container" style="width:300px;height:300px;overflow:scroll;background:#eee;">
  <div style="height:1000px;"></div>
</div>
<div>일반 이벤트 핸들러 호출 횟수: <span id="normal-count">0</span></div>
<div>쓰로틀 이벤트 핸들러 호출 횟수: <span id="throttle-count">0</span></div>
<script>
  const container = document.querySelector('.container');
  const normalCount = document.getElementById('normal-count');
  const throttleCount = document.getElementById('throttle-count');

  // 쓰로틀 함수 구현
  function throttle(callback, delay) {
    let waiting = false;
    return function (...args) {
      if (!waiting) {
        callback.apply(this, args);
        waiting = true;
        setTimeout(() => {
          waiting = false;
        }, delay);
      }
    };
  }

  let normal = 0;
  let throttled = 0;

  // 일반 이벤트 (매번 실행)
  container.addEventListener('scroll', () => {
    normalCount.textContent = ++normal;
  });

  // 쓰로틀 적용 (최대 1초에 한 번만 실행)
  container.addEventListener('scroll', throttle(() => {
    throttleCount.textContent = ++throttled;
  }, 1000));
</script>
```

**설명:**
- 일반 이벤트 핸들러는 스크롤할 때마다 무제한으로 실행됩니다.
- 쓰로틀을 적용하면 1초에 한 번만 실행되어, 불필요한 연산을 줄일 수 있습니다.

## 활용 팁
- 무한 스크롤, 차트/그래프 실시간 업데이트 등에서 성능 최적화에 필수입니다.
- 디바운스와 달리, **지정한 주기마다 꾸준히 실행**된다는 점이 특징입니다.

---

**참고**: 이 포스팅의 예제 코드는 브라우저 콘솔에서 직접 테스트해볼 수 있습니다. 궁금한 점은 댓글로 남겨주세요! 😊