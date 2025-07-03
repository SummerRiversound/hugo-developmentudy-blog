+++
title = "JavaScript 이벤트 루프와 태스크 큐 완벽 가이드 - 비동기 처리의 핵심"
date = 2024-12-16T00:00:00+09:00
draft = true
description = "JavaScript의 이벤트 루프, 태스크 큐, 마이크로태스크 큐에 대해 자세히 알아보고 비동기 처리의 동작 원리를 이해합니다."
tags = ["javascript", "이벤트루프", "비동기", "태스크큐", "마이크로태스크큐"]
categories = ["개발"]
+++

# JavaScript 이벤트 루프와 태스크 큐 완벽 가이드 - 비동기 처리의 핵심

안녕하세요! 오늘은 JavaScript의 핵심 개념 중 하나인 **이벤트 루프(Event Loop)**와 **태스크 큐(Task Queue)**에 대해 자세히 알아보려고 합니다.

JavaScript가 싱글 스레드임에도 불구하고 어떻게 비동기 작업을 처리할 수 있는지, 그리고 실제로 어떤 순서로 코드가 실행되는지 이해하는 것은 고급 JavaScript 개발자에게 필수적인 지식입니다.

## JavaScript는 싱글 스레드인가요?

### 기본 개념
JavaScript는 **싱글 스레드(Single Thread)** 언어입니다. 이는 한 번에 하나의 작업만 처리할 수 있다는 의미입니다.

하지만 실제로 웹 애플리케이션을 사용해보면:
- 애니메이션이 부드럽게 동작하면서
- 동시에 사용자 입력을 처리하고
- 서버에서 데이터를 가져오기도 합니다

이처럼 **동시성(Concurrency)**을 지원하는 것이 바로 **이벤트 루프**의 역할입니다!

## 브라우저 환경의 구조

JavaScript 엔진과 브라우저 환경이 어떻게 구성되어 있는지 살펴보겠습니다.

### 1. JavaScript 엔진 (V8)

JavaScript 엔진은 크게 두 영역으로 구성됩니다:

#### 콜 스택 (Call Stack)
```javascript
function first() {
  console.log('첫 번째 함수');
  second();
}

function second() {
  console.log('두 번째 함수');
  third();
}

function third() {
  console.log('세 번째 함수');
}

first();
```

위 코드의 콜 스택 동작 과정:

```
1. first() 호출
   ┌─────────┐
   │ first() │ ← 현재 실행 중
   └─────────┘

2. second() 호출
   ┌─────────┐
   │second() │ ← 현재 실행 중
   ├─────────┤
   │ first() │
   └─────────┘

3. third() 호출
   ┌─────────┐
   │ third() │ ← 현재 실행 중
   ├─────────┤
   │second() │
   ├─────────┤
   │ first() │
   └─────────┘

4. third() 완료
   ┌─────────┐
   │second() │ ← 현재 실행 중
   ├─────────┤
   │ first() │
   └─────────┘

5. second() 완료
   ┌─────────┐
   │ first() │ ← 현재 실행 중
   └─────────┘

6. first() 완료
   ┌─────────┐
   │ (비어있음) │
   └─────────┘
```

#### 힙 (Heap)
- 객체, 배열 등이 저장되는 메모리 공간
- 구조화되지 않은 메모리 영역
- 가비지 컬렉션이 관리하는 영역

### 2. 브라우저 환경

JavaScript 엔진 외에도 브라우저는 다음 구성 요소들을 제공합니다:

#### 태스크 큐 (Task Queue / Event Queue / Callback Queue)
```javascript
// 태스크 큐에 들어가는 예시들
setTimeout(() => console.log('타이머 완료'), 1000);
setInterval(() => console.log('인터벌'), 1000);

// 이벤트 리스너
button.addEventListener('click', () => {
  console.log('버튼 클릭됨');
});

// HTTP 요청
fetch('/api/data')
  .then(response => response.json())
  .then(data => console.log(data));
```

#### 마이크로태스크 큐 (Microtask Queue)
```javascript
// 마이크로태스크 큐에 들어가는 예시들
Promise.resolve().then(() => console.log('프로미스 완료'));
queueMicrotask(() => console.log('마이크로태스크'));

// async/await도 내부적으로 프로미스를 사용
async function asyncFunction() {
  await someAsyncOperation();
  console.log('비동기 작업 완료');
}
```

## 이벤트 루프의 동작 원리

이벤트 루프는 다음과 같은 단계를 반복합니다:

```javascript
while (true) {
  // 1. 콜 스택이 비어있는지 확인
  if (callStack.isEmpty()) {
    // 2. 마이크로태스크 큐 확인 (우선순위 높음)
    if (microtaskQueue.hasTasks()) {
      const task = microtaskQueue.dequeue();
      callStack.push(task);
      continue;
    }

    // 3. 태스크 큐 확인
    if (taskQueue.hasTasks()) {
      const task = taskQueue.dequeue();
      callStack.push(task);
      continue;
    }
  }

  // 4. 렌더링 업데이트 (필요시)
  if (needsRendering()) {
    render();
  }
}
```

## 실제 예제로 이해하기

### 예제 1: 기본적인 비동기 처리

```javascript
console.log('1. 시작');

setTimeout(() => {
  console.log('2. setTimeout 콜백');
}, 0);

Promise.resolve().then(() => {
  console.log('3. Promise 콜백');
});

console.log('4. 끝');
```

**실행 결과:**
```
1. 시작
4. 끝
3. Promise 콜백
2. setTimeout 콜백
```

**실행 과정:**
1. `console.log('1. 시작')` - 동기 실행
2. `setTimeout` - 태스크 큐에 등록 (0ms 후 실행)
3. `Promise.resolve().then()` - 마이크로태스크 큐에 등록
4. `console.log('4. 끝')` - 동기 실행
5. 콜 스택이 비어있으므로 마이크로태스크 큐 확인 → Promise 콜백 실행
6. 마이크로태스크 큐가 비어있으므로 태스크 큐 확인 → setTimeout 콜백 실행

### 예제 2: 복잡한 비동기 처리

```javascript
console.log('1. 스크립트 시작');

setTimeout(() => {
  console.log('2. 타이머 1 완료');

  Promise.resolve().then(() => {
    console.log('3. 타이머 1 내부 프로미스');
  });
}, 0);

Promise.resolve().then(() => {
  console.log('4. 프로미스 1 완료');

  setTimeout(() => {
    console.log('5. 프로미스 1 내부 타이머');
  }, 0);
});

setTimeout(() => {
  console.log('6. 타이머 2 완료');
}, 0);

Promise.resolve().then(() => {
  console.log('7. 프로미스 2 완료');
});

console.log('8. 스크립트 끝');
```

**실행 결과:**
```
1. 스크립트 시작
8. 스크립트 끝
4. 프로미스 1 완료
7. 프로미스 2 완료
2. 타이머 1 완료
3. 타이머 1 내부 프로미스
6. 타이머 2 완료
5. 프로미스 1 내부 타이머
```

## 마이크로태스크 큐 vs 태스크 큐

### 우선순위 차이

```javascript
console.log('시작');

setTimeout(() => {
  console.log('태스크 큐 - setTimeout');
}, 0);

Promise.resolve().then(() => {
  console.log('마이크로태스크 큐 - Promise');
});

queueMicrotask(() => {
  console.log('마이크로태스크 큐 - queueMicrotask');
});

console.log('끝');
```

**실행 결과:**
```
시작
끝
마이크로태스크 큐 - Promise
마이크로태스크 큐 - queueMicrotask
태스크 큐 - setTimeout
```

### 마이크로태스크 큐의 특징

```javascript
// 마이크로태스크 큐가 무한 루프에 빠질 수 있는 예시
function infiniteMicrotask() {
  Promise.resolve().then(() => {
    console.log('무한 마이크로태스크');
    infiniteMicrotask(); // 재귀 호출
  });
}

// 이 함수를 호출하면 태스크 큐의 작업들이 실행되지 않음
infiniteMicrotask();

setTimeout(() => {
  console.log('이 코드는 실행되지 않습니다');
}, 1000);
```

## 실제 활용 사례

### 1. 성능 최적화

```javascript
// 무거운 작업을 마이크로태스크로 분할
function processLargeData(data) {
  const chunkSize = 1000;
  let index = 0;

  function processChunk() {
    const chunk = data.slice(index, index + chunkSize);

    // 청크 처리
    chunk.forEach(item => {
      // 데이터 처리 로직
    });

    index += chunkSize;

    if (index < data.length) {
      // 다음 청크를 마이크로태스크로 예약
      Promise.resolve().then(processChunk);
    } else {
      console.log('모든 데이터 처리 완료');
    }
  }

  processChunk();
}

// 사용 예시
const largeData = Array.from({ length: 10000 }, (_, i) => i);
processLargeData(largeData);
```

### 2. 이벤트 처리 최적화

```javascript
// 디바운싱과 마이크로태스크 활용
function debounce(func, delay) {
  let timeoutId;

  return function (...args) {
    clearTimeout(timeoutId);

    timeoutId = setTimeout(() => {
      // 마이크로태스크로 실행하여 UI 업데이트 우선
      Promise.resolve().then(() => {
        func.apply(this, args);
      });
    }, delay);
  };
}

// 사용 예시
const debouncedSearch = debounce((query) => {
  console.log('검색 실행:', query);
}, 300);

// 입력 이벤트에서 사용
input.addEventListener('input', (e) => {
  debouncedSearch(e.target.value);
});
```

### 3. 상태 업데이트 최적화

```javascript
// React와 유사한 상태 업데이트 배치 처리
class StateManager {
  constructor() {
    this.state = {};
    this.pendingUpdates = new Set();
    this.isUpdating = false;
  }

  setState(key, value) {
    this.pendingUpdates.add(key);
    this.state[key] = value;

    if (!this.isUpdating) {
      this.isUpdating = true;

      // 마이크로태스크로 배치 업데이트 실행
      Promise.resolve().then(() => {
        this.flushUpdates();
      });
    }
  }

  flushUpdates() {
    console.log('배치 업데이트 실행:', Array.from(this.pendingUpdates));
    this.pendingUpdates.clear();
    this.isUpdating = false;
  }
}

// 사용 예시
const stateManager = new StateManager();

// 여러 상태 업데이트가 동시에 발생해도 한 번만 실행됨
stateManager.setState('user', { name: '김개발' });
stateManager.setState('theme', 'dark');
stateManager.setState('loading', false);
```

## 디버깅 팁

### 1. 실행 순서 확인

```javascript
// 실행 순서를 추적하는 유틸리티
function traceExecution(label) {
  console.log(`[${Date.now()}] ${label}`);
}

traceExecution('스크립트 시작');

setTimeout(() => {
  traceExecution('setTimeout 실행');
}, 0);

Promise.resolve().then(() => {
  traceExecution('Promise 실행');
});

traceExecution('스크립트 끝');
```

### 2. 비동기 작업 모니터링

```javascript
// 비동기 작업 추적
class AsyncTracker {
  constructor() {
    this.tasks = new Map();
    this.taskId = 0;
  }

  track(task, label) {
    const id = ++this.taskId;
    this.tasks.set(id, { label, startTime: Date.now() });

    console.log(`[${id}] ${label} 시작`);

    return id;
  }

  complete(id) {
    const task = this.tasks.get(id);
    if (task) {
      const duration = Date.now() - task.startTime;
      console.log(`[${id}] ${task.label} 완료 (${duration}ms)`);
      this.tasks.delete(id);
    }
  }
}

const tracker = new AsyncTracker();

// 사용 예시
const task1 = tracker.track(setTimeout(() => {
  tracker.complete(task1);
}, 1000), '타이머 작업');

const task2 = tracker.track(Promise.resolve().then(() => {
  tracker.complete(task2);
}), '프로미스 작업');
```

## 성능 고려사항

### 1. 마이크로태스크 큐 주의사항

```javascript
// ❌ 잘못된 예시 - 무한 루프
function badExample() {
  Promise.resolve().then(() => {
    console.log('무한 실행');
    badExample(); // 재귀 호출로 인한 무한 루프
  });
}

// ✅ 올바른 예시 - 제한된 실행
function goodExample(maxCount = 1000) {
  let count = 0;

  function process() {
    if (count >= maxCount) {
      console.log('처리 완료');
      return;
    }

    count++;
    console.log(`처리 중: ${count}`);

    // 다음 실행을 태스크 큐로 예약
    setTimeout(process, 0);
  }

  process();
}
```

### 2. 렌더링 블로킹 방지

```javascript
// ❌ 렌더링을 블로킹하는 코드
function blockingOperation() {
  const start = Date.now();
  while (Date.now() - start < 100) {
    // 100ms 동안 블로킹
  }
}

// ✅ 렌더링을 블로킹하지 않는 코드
function nonBlockingOperation() {
  const start = Date.now();

  function process() {
    if (Date.now() - start < 100) {
      // 다음 프레임에서 계속 실행
      requestAnimationFrame(process);
    } else {
      console.log('작업 완료');
    }
  }

  process();
}
```

## 마치며

이벤트 루프와 태스크 큐는 JavaScript의 비동기 처리 핵심 메커니즘입니다. 이 개념을 제대로 이해하면:

- **예측 가능한 코드 작성**: 실행 순서를 정확히 예측할 수 있습니다
- **성능 최적화**: 적절한 큐를 선택하여 성능을 개선할 수 있습니다
- **디버깅 능력 향상**: 비동기 관련 버그를 쉽게 찾고 수정할 수 있습니다

실제 프로젝트에서 이 지식을 활용하여 더 나은 JavaScript 애플리케이션을 만들어보세요!

### 추가 학습 자료
- [MDN 이벤트 루프](https://developer.mozilla.org/ko/docs/Web/JavaScript/EventLoop)
- [JavaScript Visualized: Event Loop](https://dev.to/lydiahallie/javascript-visualized-event-loop-3dif)
- [Philip Roberts: What the heck is the event loop anyway?](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

---

**참고**: 이 포스팅의 모든 예제는 브라우저 콘솔에서 직접 실행해볼 수 있습니다. 실제로 코드를 실행해보면서 이벤트 루프의 동작을 관찰해보세요! 😊