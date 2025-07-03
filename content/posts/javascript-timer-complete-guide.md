+++
title = "JavaScript 타이머 완벽 가이드 - setTimeout, setInterval 마스터하기"
date = 2024-12-19T00:00:00+09:00
draft = false
description = "JavaScript의 타이머 함수들(setTimeout, setInterval)의 개념, 사용법, 실전 예제와 주의사항까지 완벽하게 정리합니다."
tags = ["javascript", "타이머", "setTimeout", "setInterval", "비동기", "스케줄링"]
categories = ["개발"]
+++

# JavaScript 타이머 완벽 가이드 - setTimeout, setInterval 마스터하기

안녕하세요! 오늘은 JavaScript의 핵심 기능 중 하나인 **타이머 함수들**에 대해 자세히 알아보겠습니다.

웹 개발에서 타이머는 애니메이션, 자동 저장, 폴링, 지연 실행 등 다양한 용도로 사용되는 필수적인 기능입니다. 이 포스팅에서는 `setTimeout`, `setInterval`의 기본 개념부터 실전 활용까지 체계적으로 정리해보겠습니다!

## 호출 스케줄링이란?

### 기본 개념
**호출 스케줄링(Call Scheduling)**은 함수를 명시적으로 호출하지 않고, 일정 시간이 경과된 후에 자동으로 실행되도록 예약하는 것을 말합니다.

### 일반적인 함수 호출 vs 스케줄링
```javascript
// 일반적인 함수 호출 (즉시 실행)
function add(a, b) {
  return a + b;
}
console.log(add(2, 5)); // 즉시 실행: 7

// 스케줄링된 함수 호출 (지연 실행)
setTimeout(() => {
  console.log(add(2, 5)); // 1초 후 실행: 7
}, 1000);
```

만약 함수를 명시적으로 호출하지 않고 일정 시간이 경과된 이후에 호출되도록 함수 호출을 예약하려면, 타이머 함수를 사용해야 합니다.

이렇게 **타이머 함수를 사용하여 명시적으로 호출하지 않고 일정 시간이 경과된 이후에 호출되도록 함수 호출을 예약하는 것**을 **호출 스케줄링이라고 합니다.**

## 타이머 함수의 종류

JavaScript에는 두 가지 주요 타이머 함수가 있습니다:

1. **setTimeout/clearTimeout**: 한 번만 실행되는 타이머
2. **setInterval/clearInterval**: 반복 실행되는 타이머

## setTimeout - 한 번 실행되는 타이머

### 기본 문법
```javascript
const timeoutId = setTimeout(func|code[, delay, param1, param2, ...]);
```

### 매개변수 설명
| 매개변수 | 설명 |
|----------|------|
| `func` | 타이머가 만료된 뒤 호출될 콜백 함수. 콜백 함수 대신 코드를 문자열로 전달할 수 있다. 이때 코드 문자열은 타이머가 만료된 뒤 해석되고 실행된다. |
| `delay` | 타이머 만료 시간(밀리초(ms) 단위). setTimeout 함수는 delay 시간으로 단 한 번 동작하는 타이머를 생성한다. 인수 전달을 생략한 경우 기본값 0이 지정된다. |
| `param1, param2, ...` | 호출 스케줄링된 콜백 함수에 전달해야 할 인수가 존재하는 경우 세 번째 이후의 인수로 전달할 수 있다. |

setTimeout 함수는 생성된 타이머를 식별할 수 있는 고유한 id를 반환합니다.

setTimeout 함수가 반환한 타이머 id는:
- **브라우저 환경**: 숫자
- **Node.js 환경**: 객체

### 기본 사용법
```javascript
// 1초(1000ms) 후 타이머가 만료되면 콜백 함수가 호출된다
setTimeout(() => console.log('Hi!'), 1000);

// 세 번째 인수로 문자열 'Lee' 전달
setTimeout((name) => console.log(`Hi! ${name}.`), 1000, 'Lee');

// 두 번째 인수(delay)를 생략하면 기본값 0이 지정된다
setTimeout(() => console.log('Hello!'));
```

### 타이머 취소하기
setTimeout 함수가 반환한 타이머 id를 clearTimeout 함수의 인수로 전달하여 타이머를 취소할 수 있습니다.

```javascript
const timerId = setTimeout(() => console.log('Hi!'), 1000);
console.log(timerId);
clearTimeout(timerId);
```

## setInterval - 반복 실행되는 타이머

### 기본 문법
```javascript
const intervalId = setInterval(func|code[, delay, param1, param2, ...]);
```

setInterval 함수는 두 번째 인수로 전달받은 시간(ms, 1/1000초)으로 반복 동작하는 타이머를 생성합니다.

setInterval의 첫 번째 인수인 콜백 함수는 두 번째 인수로 전달받은 시간이 경과할 때마다 반복 실행되도록 호출 스케줄링됩니다.

### 기본 사용법
```javascript
// 1초마다 반복 실행
const intervalId = setInterval(() => {
  console.log('1초마다 실행됨!');
}, 1000);

// 5초 후 타이머 중지
setTimeout(() => {
  clearInterval(intervalId);
  console.log('반복 타이머가 중지되었습니다');
}, 5000);
```

### 매개변수 전달
```javascript
let count = 0;
const intervalId = setInterval((message) => {
  count++;
  console.log(`${message} ${count}번째 실행`);

  if (count >= 5) {
    clearInterval(intervalId);
    console.log('5번 실행 완료!');
  }
}, 1000, '카운터:');
```

## 실전 활용 사례

### 1. 자동 저장 기능
```javascript
class AutoSave {
  constructor() {
    this.saveTimeout = null;
    this.setupAutoSave();
  }

  setupAutoSave() {
    const textarea = document.getElementById('content');

    textarea.addEventListener('input', () => {
      // 이전 타이머 취소
      if (this.saveTimeout) {
        clearTimeout(this.saveTimeout);
      }

      // 2초 후 자동 저장
      this.saveTimeout = setTimeout(() => {
        this.saveContent();
      }, 2000);
    });
  }

  async saveContent() {
    const content = document.getElementById('content').value;

    try {
      await fetch('/api/save', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ content })
      });

      console.log('자동 저장 완료!');
    } catch (error) {
      console.error('저장 실패:', error);
    }
  }
}

// 사용 예시
const autoSave = new AutoSave();
```

### 2. 카운트다운 타이머
```javascript
class CountdownTimer {
  constructor(duration, onComplete) {
    this.duration = duration;
    this.onComplete = onComplete;
    this.remaining = duration;
    this.intervalId = null;
  }

  start() {
    this.updateDisplay();

    this.intervalId = setInterval(() => {
      this.remaining--;
      this.updateDisplay();

      if (this.remaining <= 0) {
        this.stop();
        this.onComplete();
      }
    }, 1000);
  }

  stop() {
    if (this.intervalId) {
      clearInterval(this.intervalId);
      this.intervalId = null;
    }
  }

  updateDisplay() {
    const minutes = Math.floor(this.remaining / 60);
    const seconds = this.remaining % 60;

    const display = document.getElementById('countdown');
    if (display) {
      display.textContent = `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
    }
  }
}

// 사용 예시
const timer = new CountdownTimer(60, () => {
  console.log('시간이 종료되었습니다!');
});
timer.start();
```

### 3. 폴링 (Polling) 구현
```javascript
class PollingService {
  constructor(url, interval = 5000) {
    this.url = url;
    this.interval = interval;
    this.intervalId = null;
    this.isPolling = false;
  }

  start() {
    if (this.isPolling) return;

    this.isPolling = true;
    this.poll();

    this.intervalId = setInterval(() => {
      this.poll();
    }, this.interval);
  }

  stop() {
    this.isPolling = false;
    if (this.intervalId) {
      clearInterval(this.intervalId);
      this.intervalId = null;
    }
  }

  async poll() {
    try {
      const response = await fetch(this.url);
      const data = await response.json();

      this.handleData(data);
    } catch (error) {
      console.error('폴링 오류:', error);
    }
  }

  handleData(data) {
    // 데이터 처리 로직
    console.log('새로운 데이터:', data);
  }
}

// 사용 예시
const pollingService = new PollingService('/api/updates', 3000);
pollingService.start();

// 30초 후 폴링 중지
setTimeout(() => {
  pollingService.stop();
}, 30000);
```

### 4. 애니메이션 구현
```javascript
class AnimationController {
  constructor(element) {
    this.element = element;
    this.animationId = null;
  }

  fadeIn(duration = 1000) {
    this.element.style.opacity = '0';
    this.element.style.display = 'block';

    const startTime = Date.now();

    const animate = () => {
      const elapsed = Date.now() - startTime;
      const progress = Math.min(elapsed / duration, 1);

      this.element.style.opacity = progress;

      if (progress < 1) {
        this.animationId = setTimeout(animate, 16); // 약 60fps
      }
    };

    animate();
  }

  fadeOut(duration = 1000) {
    const startTime = Date.now();

    const animate = () => {
      const elapsed = Date.now() - startTime;
      const progress = Math.min(elapsed / duration, 1);

      this.element.style.opacity = 1 - progress;

      if (progress < 1) {
        this.animationId = setTimeout(animate, 16);
      } else {
        this.element.style.display = 'none';
      }
    };

    animate();
  }

  stop() {
    if (this.animationId) {
      clearTimeout(this.animationId);
      this.animationId = null;
    }
  }
}

// 사용 예시
const modal = document.getElementById('modal');
const animator = new AnimationController(modal);

// 페이드 인
animator.fadeIn(500);

// 3초 후 페이드 아웃
setTimeout(() => {
  animator.fadeOut(500);
}, 3000);
```

## 고급 활용 기법

### 1. Promise 기반 타이머
```javascript
// Promise로 감싼 setTimeout
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// 사용 예시
async function example() {
  console.log('시작');
  await delay(2000);
  console.log('2초 후 실행');
}

// 여러 타이머를 순차적으로 실행
async function sequentialTimers() {
  console.log('1단계');
  await delay(1000);

  console.log('2단계');
  await delay(1000);

  console.log('3단계');
  await delay(1000);

  console.log('완료!');
}
```

### 2. 조건부 타이머
```javascript
class ConditionalTimer {
  constructor() {
    this.timers = new Map();
  }

  // 조건이 만족될 때까지 대기
  waitFor(condition, timeout = 10000) {
    return new Promise((resolve, reject) => {
      const startTime = Date.now();

      const checkCondition = () => {
        if (condition()) {
          resolve();
          return;
        }

        if (Date.now() - startTime > timeout) {
          reject(new Error('타임아웃'));
          return;
        }

        setTimeout(checkCondition, 100);
      };

      checkCondition();
    });
  }

  // 재시도 로직
  retry(fn, maxAttempts = 3, delay = 1000) {
    return new Promise((resolve, reject) => {
      let attempts = 0;

      const attempt = () => {
        attempts++;

        fn()
          .then(resolve)
          .catch(error => {
            if (attempts >= maxAttempts) {
              reject(error);
            } else {
              setTimeout(attempt, delay);
            }
          });
      };

      attempt();
    });
  }
}

// 사용 예시
const conditionalTimer = new ConditionalTimer();

// 요소가 로드될 때까지 대기
conditionalTimer.waitFor(() => document.getElementById('myElement'))
  .then(() => console.log('요소가 로드되었습니다!'));

// API 호출 재시도
conditionalTimer.retry(() => fetch('/api/data'), 3, 2000)
  .then(data => console.log('데이터 로드 성공:', data))
  .catch(error => console.error('모든 시도 실패:', error));
```

## 주의사항과 모범 사례

### 1. 메모리 누수 방지
```javascript
class TimerManager {
  constructor() {
    this.timers = new Set();
  }

  setTimeout(callback, delay, ...args) {
    const timerId = setTimeout(() => {
      this.timers.delete(timerId);
      callback(...args);
    }, delay);

    this.timers.add(timerId);
    return timerId;
  }

  setInterval(callback, delay, ...args) {
    const intervalId = setInterval(callback, delay, ...args);
    this.timers.add(intervalId);
    return intervalId;
  }

  clearTimeout(timerId) {
    clearTimeout(timerId);
    this.timers.delete(timerId);
  }

  clearInterval(intervalId) {
    clearInterval(intervalId);
    this.timers.delete(intervalId);
  }

  // 모든 타이머 정리
  clearAll() {
    this.timers.forEach(timerId => {
      clearTimeout(timerId);
      clearInterval(timerId);
    });
    this.timers.clear();
  }
}

// 사용 예시
const timerManager = new TimerManager();

// 컴포넌트 언마운트 시 정리
function cleanup() {
  timerManager.clearAll();
}
```

### 2. 정확한 시간 보장
```javascript
// 정확한 시간을 보장하는 타이머
class PreciseTimer {
  constructor() {
    this.startTime = null;
    this.intervalId = null;
  }

  start(callback, interval) {
    this.startTime = Date.now();

    const tick = () => {
      const elapsed = Date.now() - this.startTime;
      const drift = elapsed % interval;

      callback();

      // 드리프트 보정
      this.intervalId = setTimeout(tick, interval - drift);
    };

    tick();
  }

  stop() {
    if (this.intervalId) {
      clearTimeout(this.intervalId);
      this.intervalId = null;
    }
  }
}

// 사용 예시
const preciseTimer = new PreciseTimer();
preciseTimer.start(() => {
  console.log('정확한 1초 간격:', new Date().toISOString());
}, 1000);
```

### 3. 브라우저 탭 비활성화 시 주의사항
```javascript
// 페이지가 비활성화될 때 타이머 일시정지
class BackgroundAwareTimer {
  constructor() {
    this.isPageVisible = true;
    this.setupVisibilityListener();
  }

  setupVisibilityListener() {
    document.addEventListener('visibilitychange', () => {
      this.isPageVisible = !document.hidden;
    });
  }

  setTimeout(callback, delay) {
    if (!this.isPageVisible) {
      // 페이지가 비활성화된 경우 지연 실행
      delay += 1000;
    }

    return setTimeout(callback, delay);
  }

  setInterval(callback, delay) {
    return setInterval(() => {
      if (this.isPageVisible) {
        callback();
      }
    }, delay);
  }
}
```

## 디버깅 팁

### 1. 타이머 추적
```javascript
class TimerTracker {
  constructor() {
    this.activeTimers = new Map();
  }

  setTimeout(callback, delay, ...args) {
    const timerId = setTimeout(() => {
      this.activeTimers.delete(timerId);
      callback(...args);
    }, delay);

    this.activeTimers.set(timerId, {
      type: 'timeout',
      delay,
      startTime: Date.now(),
      callback: callback.toString()
    });

    return timerId;
  }

  setInterval(callback, delay, ...args) {
    const intervalId = setInterval(callback, delay, ...args);

    this.activeTimers.set(intervalId, {
      type: 'interval',
      delay,
      startTime: Date.now(),
      callback: callback.toString()
    });

    return intervalId;
  }

  getActiveTimers() {
    return Array.from(this.activeTimers.entries()).map(([id, info]) => ({
      id,
      ...info,
      elapsed: Date.now() - info.startTime
    }));
  }

  logActiveTimers() {
    console.table(this.getActiveTimers());
  }
}

// 사용 예시
const tracker = new TimerTracker();

tracker.setTimeout(() => console.log('Hello'), 5000);
tracker.setInterval(() => console.log('Tick'), 1000);

// 2초 후 활성 타이머 확인
setTimeout(() => {
  tracker.logActiveTimers();
}, 2000);
```

## 마치며

JavaScript 타이머는 웹 개발에서 매우 중요한 기능입니다. 올바르게 사용하면 사용자 경험을 크게 향상시킬 수 있지만, 잘못 사용하면 성능 문제나 메모리 누수를 일으킬 수 있습니다.

### 핵심 포인트:
- **setTimeout**: 한 번 실행되는 지연 함수
- **setInterval**: 반복 실행되는 주기 함수
- **메모리 관리**: 타이머 정리 필수
- **정확성**: 드리프트 보정 고려
- **사용자 경험**: 적절한 타이밍 설정

실제 프로젝트에서 이 지식을 활용하여 더 나은 웹 애플리케이션을 만들어보세요!

### 추가 학습 자료
- [MDN setTimeout](https://developer.mozilla.org/ko/docs/Web/API/setTimeout)
- [MDN setInterval](https://developer.mozilla.org/ko/docs/Web/API/setInterval)
- [JavaScript Timing Events](https://www.w3schools.com/js/js_timing.asp)

---

**참고**: 이 포스팅의 모든 예제는 브라우저 콘솔에서 직접 실행해볼 수 있습니다. 궁금한 점이나 추가로 알고 싶은 내용이 있다면 댓글로 남겨주세요! 😊