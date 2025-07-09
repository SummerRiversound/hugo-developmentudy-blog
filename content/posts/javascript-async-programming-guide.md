+++
title = "JavaScript 비동기 프로그래밍 완벽 가이드 - 동기 vs 비동기 이해하기"
date = 2025-07-09T08:02:01+09:00
draft = false
description = "JavaScript의 동기와 비동기 처리 방식의 차이점을 이해하고, 실제 활용 사례를 통해 비동기 프로그래밍을 마스터합니다."
tags = ["javascript", "비동기", "동기", "async", "await", "promise"]
categories = ["개발"]
+++

# JavaScript 비동기 프로그래밍 완벽 가이드 - 동기 vs 비동기 이해하기

JavaScript 개발에서 가장 중요한 개념 중 하나인 **비동기 프로그래밍**에 대해 써보려합니다.

웹 개발을 하다 보면 "동기"와 "비동기"라는 용어를 자주 접하게 되는데, 이 두 개념을 제대로 이해하는 것이 현대 JavaScript 개발의 핵심입니다.

## 동기 vs 비동기란?

### 기본 개념

JavaScript는 **싱글 스레드(Single Thread)** 언어입니다. 이는 한 번에 하나의 작업만 처리할 수 있다는 의미입니다. 하지만 실제로는 여러 작업이 동시에 처리되는 것처럼 보이는데, 이는 **동기**와 **비동기** 처리 방식의 차이 때문입니다.

### 동기(Synchronous) 처리

**동기 처리**는 현재 실행 중인 작업이 완료될 때까지 다음 작업이 대기하는 방식입니다.

```javascript
// 동기 처리 예시
function syncTask() {
  console.log('1. 첫 번째 작업 시작');

  // 시간이 오래 걸리는 작업 (3초)
  const start = Date.now();
  while (Date.now() - start < 3000) {
    // 3초 동안 대기
  }

  console.log('2. 첫 번째 작업 완료');
}

function secondTask() {
  console.log('3. 두 번째 작업 실행');
}

syncTask();  // 3초 대기
secondTask(); // 첫 번째 작업 완료 후 실행
```

**실행 결과:**
```
1. 첫 번째 작업 시작
(3초 대기)
2. 첫 번째 작업 완료
3. 두 번째 작업 실행
```

### 비동기(Asynchronous) 처리

**비동기 처리**는 현재 실행 중인 작업이 완료되지 않아도 다음 작업을 바로 실행하는 방식입니다.

```javascript
// 비동기 처리 예시
function asyncTask() {
  console.log('1. 비동기 작업 시작');

  // setTimeout은 비동기로 동작
  setTimeout(() => {
    console.log('2. 비동기 작업 완료');
  }, 3000);

  console.log('3. 비동기 작업 등록 완료');
}

function secondTask() {
  console.log('4. 두 번째 작업 실행');
}

asyncTask();  // 비동기 작업 등록
secondTask(); // 바로 실행됨
```

**실행 결과:**
```
1. 비동기 작업 시작
3. 비동기 작업 등록 완료
4. 두 번째 작업 실행
(3초 후)
2. 비동기 작업 완료
```

## JavaScript의 실행 컨텍스트와 스택

### 실행 컨텍스트 스택 (Call Stack)

JavaScript 엔진은 **실행 컨텍스트 스택**을 통해 함수의 실행을 관리합니다.

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

**실행 컨텍스트 스택 동작:**

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

### 싱글 스레드의 한계

JavaScript는 단 하나의 실행 컨텍스트 스택을 가지므로, 동시에 여러 함수를 실행할 수 없습니다.

```javascript
// ❌ 문제가 되는 동기 처리 예시
function blockingTask() {
  console.log('무거운 작업 시작');

  // 5초 동안 블로킹
  const start = Date.now();
  while (Date.now() - start < 5000) {
    // 아무것도 하지 않음
  }

  console.log('무거운 작업 완료');
}

function userInteraction() {
  console.log('사용자 상호작용 처리');
}

blockingTask();  // 5초 동안 블로킹
userInteraction(); // 5초 후에 실행됨
```

이 경우 사용자는 5초 동안 아무것도 할 수 없게 됩니다!

## 비동기 처리의 필요성

### 실제 웹 애플리케이션에서의 문제

```javascript
// ❌ 동기 방식의 API 호출 (문제가 있는 코드)
function fetchUserDataSync(userId) {
  console.log('사용자 데이터 요청 시작');

  // 실제로는 이런 동기 HTTP 요청은 불가능
  const response = fetch(`/api/users/${userId}`); // 이 부분이 블로킹됨
  const userData = response.json();

  console.log('사용자 데이터:', userData);
  return userData;
}

// 이 함수가 실행되면 전체 페이지가 멈춤
const userData = fetchUserDataSync(123);
console.log('다른 작업'); // 위 함수가 완료될 때까지 실행되지 않음
```

### 비동기 방식으로 해결

```javascript
// ✅ 비동기 방식의 API 호출
function fetchUserDataAsync(userId) {
  console.log('사용자 데이터 요청 시작');

  return fetch(`/api/users/${userId}`)
    .then(response => response.json())
    .then(userData => {
      console.log('사용자 데이터:', userData);
      return userData;
    });
}

// 비동기 호출
fetchUserDataAsync(123);
console.log('다른 작업'); // 즉시 실행됨
```

## JavaScript의 비동기 처리 방식

### 1. 콜백(Callback)

가장 기본적인 비동기 처리 방식입니다.

```javascript
// 콜백을 사용한 비동기 처리
function fetchData(callback) {
  console.log('데이터 요청 시작');

  setTimeout(() => {
    const data = { id: 1, name: '김개발' };
    console.log('데이터 수신 완료');
    callback(data);
  }, 2000);
}

// 사용 예시
fetchData((data) => {
  console.log('받은 데이터:', data);
});

console.log('다른 작업 실행');
```

**실행 결과:**
```
데이터 요청 시작
다른 작업 실행
(2초 후)
데이터 수신 완료
받은 데이터: { id: 1, name: '김개발' }
```

### 2. Promise

콜백의 단점을 해결하기 위해 등장한 방식입니다.

```javascript
// Promise를 사용한 비동기 처리
function fetchDataPromise() {
  return new Promise((resolve, reject) => {
    console.log('데이터 요청 시작');

    setTimeout(() => {
      const data = { id: 1, name: '김개발' };
      console.log('데이터 수신 완료');
      resolve(data);
    }, 2000);
  });
}

// 사용 예시
fetchDataPromise()
  .then(data => {
    console.log('받은 데이터:', data);
    return data.id;
  })
  .then(id => {
    console.log('사용자 ID:', id);
  })
  .catch(error => {
    console.error('오류 발생:', error);
  });

console.log('다른 작업 실행');
```

### 3. async/await

Promise를 더 쉽게 사용할 수 있게 해주는 문법입니다.

```javascript
// async/await를 사용한 비동기 처리
async function fetchUserData() {
  try {
    console.log('사용자 데이터 요청 시작');

    const response = await fetch('/api/users/123');
    const userData = await response.json();

    console.log('사용자 데이터:', userData);
    return userData;
  } catch (error) {
    console.error('오류 발생:', error);
    throw error;
  }
}

// 사용 예시
async function main() {
  const userData = await fetchUserData();
  console.log('메인 함수에서 받은 데이터:', userData);
}

main();
console.log('다른 작업 실행');
```

## 실제 활용 사례

### 1. 파일 업로드 처리

```javascript
// 파일 업로드 비동기 처리
class FileUploader {
  constructor() {
    this.uploadQueue = [];
    this.isUploading = false;
  }

  async uploadFile(file) {
    return new Promise((resolve, reject) => {
      const formData = new FormData();
      formData.append('file', file);

      fetch('/api/upload', {
        method: 'POST',
        body: formData
      })
      .then(response => response.json())
      .then(result => {
        console.log('파일 업로드 완료:', result);
        resolve(result);
      })
      .catch(error => {
        console.error('파일 업로드 실패:', error);
        reject(error);
      });
    });
  }

  async uploadMultipleFiles(files) {
    const uploadPromises = Array.from(files).map(file =>
      this.uploadFile(file)
    );

    try {
      const results = await Promise.all(uploadPromises);
      console.log('모든 파일 업로드 완료:', results);
      return results;
    } catch (error) {
      console.error('일부 파일 업로드 실패:', error);
      throw error;
    }
  }
}

// 사용 예시
const uploader = new FileUploader();
const fileInput = document.getElementById('fileInput');

fileInput.addEventListener('change', async (event) => {
  const files = event.target.files;

  try {
    const results = await uploader.uploadMultipleFiles(files);
    console.log('업로드 성공:', results);
  } catch (error) {
    console.error('업로드 실패:', error);
  }
});
```

### 2. 데이터베이스 작업

```javascript
// 데이터베이스 비동기 작업
class UserService {
  async getUsers() {
    try {
      const response = await fetch('/api/users');
      return await response.json();
    } catch (error) {
      console.error('사용자 목록 조회 실패:', error);
      throw error;
    }
  }

  async createUser(userData) {
    try {
      const response = await fetch('/api/users', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(userData)
      });

      return await response.json();
    } catch (error) {
      console.error('사용자 생성 실패:', error);
      throw error;
    }
  }

  async updateUser(id, userData) {
    try {
      const response = await fetch(`/api/users/${id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(userData)
      });

      return await response.json();
    } catch (error) {
      console.error('사용자 수정 실패:', error);
      throw error;
    }
  }

  async deleteUser(id) {
    try {
      await fetch(`/api/users/${id}`, {
        method: 'DELETE'
      });

      console.log('사용자 삭제 완료');
    } catch (error) {
      console.error('사용자 삭제 실패:', error);
      throw error;
    }
  }
}

// 사용 예시
const userService = new UserService();

async function manageUsers() {
  try {
    // 사용자 목록 조회
    const users = await userService.getUsers();
    console.log('사용자 목록:', users);

    // 새 사용자 생성
    const newUser = await userService.createUser({
      name: '김개발',
      email: 'dev@example.com'
    });
    console.log('생성된 사용자:', newUser);

    // 사용자 정보 수정
    const updatedUser = await userService.updateUser(newUser.id, {
      name: '김개발',
      email: 'developer@example.com'
    });
    console.log('수정된 사용자:', updatedUser);

  } catch (error) {
    console.error('사용자 관리 중 오류:', error);
  }
}

manageUsers();
```

### 3. 애니메이션과 비동기 처리

```javascript
// 애니메이션과 비동기 처리
class AnimationController {
  async fadeIn(element, duration = 1000) {
    return new Promise((resolve) => {
      element.style.opacity = '0';
      element.style.display = 'block';

      let start = null;

      function animate(timestamp) {
        if (!start) start = timestamp;
        const progress = timestamp - start;
        const opacity = Math.min(progress / duration, 1);

        element.style.opacity = opacity;

        if (progress < duration) {
          requestAnimationFrame(animate);
        } else {
          resolve();
        }
      }

      requestAnimationFrame(animate);
    });
  }

  async fadeOut(element, duration = 1000) {
    return new Promise((resolve) => {
      let start = null;

      function animate(timestamp) {
        if (!start) start = timestamp;
        const progress = timestamp - start;
        const opacity = Math.max(1 - progress / duration, 0);

        element.style.opacity = opacity;

        if (progress < duration) {
          requestAnimationFrame(animate);
        } else {
          element.style.display = 'none';
          resolve();
        }
      }

      requestAnimationFrame(animate);
    });
  }

  async slideIn(element, direction = 'left', duration = 1000) {
    return new Promise((resolve) => {
      const originalTransform = element.style.transform;
      const originalDisplay = element.style.display;

      element.style.display = 'block';

      let start = null;
      const distance = direction === 'left' || direction === 'right' ?
        element.offsetWidth : element.offsetHeight;

      const translateValue = direction === 'left' ? -distance :
                           direction === 'right' ? distance :
                           direction === 'up' ? -distance : distance;

      element.style.transform = `translate${direction === 'left' || direction === 'right' ? 'X' : 'Y'}(${translateValue}px)`;

      function animate(timestamp) {
        if (!start) start = timestamp;
        const progress = timestamp - start;
        const ratio = Math.min(progress / duration, 1);

        const currentTranslate = translateValue * (1 - ratio);
        element.style.transform = `translate${direction === 'left' || direction === 'right' ? 'X' : 'Y'}(${currentTranslate}px)`;

        if (progress < duration) {
          requestAnimationFrame(animate);
        } else {
          element.style.transform = originalTransform;
          resolve();
        }
      }

      requestAnimationFrame(animate);
    });
  }
}

// 사용 예시
const animationController = new AnimationController();
const modal = document.getElementById('modal');

async function showModal() {
  await animationController.fadeIn(modal);
  console.log('모달 표시 완료');
}

async function hideModal() {
  await animationController.fadeOut(modal);
  console.log('모달 숨김 완료');
}

// 버튼 클릭 시 모달 표시/숨김
document.getElementById('showModal').addEventListener('click', showModal);
document.getElementById('hideModal').addEventListener('click', hideModal);
```

## 비동기 처리의 주의사항

### 1. 콜백 지옥(Callback Hell)

```javascript
// ❌ 콜백 지옥 예시
fetchUserData((user) => {
  fetchUserPosts(user.id, (posts) => {
    fetchPostComments(posts[0].id, (comments) => {
      fetchCommentAuthor(comments[0].authorId, (author) => {
        console.log('작성자 정보:', author);
      });
    });
  });
});

// ✅ Promise로 해결
fetchUserData()
  .then(user => fetchUserPosts(user.id))
  .then(posts => fetchPostComments(posts[0].id))
  .then(comments => fetchCommentAuthor(comments[0].authorId))
  .then(author => console.log('작성자 정보:', author))
  .catch(error => console.error('오류:', error));

// ✅ async/await로 더 깔끔하게 해결
async function getCommentAuthor() {
  try {
    const user = await fetchUserData();
    const posts = await fetchUserPosts(user.id);
    const comments = await fetchPostComments(posts[0].id);
    const author = await fetchCommentAuthor(comments[0].authorId);

    console.log('작성자 정보:', author);
    return author;
  } catch (error) {
    console.error('오류:', error);
    throw error;
  }
}
```

### 2. 에러 처리

```javascript
// 비동기 함수의 에러 처리
async function handleAsyncOperation() {
  try {
    const result = await riskyAsyncOperation();
    return result;
  } catch (error) {
    console.error('비동기 작업 실패:', error);

    // 에러 복구 시도
    try {
      const fallbackResult = await fallbackOperation();
      return fallbackResult;
    } catch (fallbackError) {
      console.error('복구 작업도 실패:', fallbackError);
      throw new Error('모든 작업이 실패했습니다');
    }
  }
}

// Promise.all의 에러 처리
async function handleMultipleOperations() {
  const promises = [
    fetch('/api/users'),
    fetch('/api/posts'),
    fetch('/api/comments')
  ];

  try {
    const results = await Promise.all(promises);
    return results.map(response => response.json());
  } catch (error) {
    console.error('일부 요청 실패:', error);

    // 일부 실패해도 성공한 결과는 처리
    const results = await Promise.allSettled(promises);
    const successfulResults = results
      .filter(result => result.status === 'fulfilled')
      .map(result => result.value);

    return successfulResults;
  }
}
```

### 3. 메모리 누수 방지

```javascript
// 이벤트 리스너 정리
class EventManager {
  constructor() {
    this.listeners = new Map();
  }

  addEventListener(element, event, handler) {
    element.addEventListener(event, handler);

    if (!this.listeners.has(element)) {
      this.listeners.set(element, []);
    }
    this.listeners.get(element).push({ event, handler });
  }

  removeAllListeners(element) {
    const elementListeners = this.listeners.get(element);
    if (elementListeners) {
      elementListeners.forEach(({ event, handler }) => {
        element.removeEventListener(event, handler);
      });
      this.listeners.delete(element);
    }
  }

  cleanup() {
    this.listeners.forEach((listeners, element) => {
      this.removeAllListeners(element);
    });
  }
}

// 사용 예시
const eventManager = new EventManager();

// 이벤트 리스너 추가
eventManager.addEventListener(button, 'click', handleClick);
eventManager.addEventListener(input, 'input', handleInput);

// 컴포넌트 언마운트 시 정리
function cleanup() {
  eventManager.cleanup();
}
```

## 성능 최적화 팁

### 1. 병렬 처리

```javascript
// 순차 처리 vs 병렬 처리
async function sequentialProcessing() {
  const start = Date.now();

  const result1 = await fetch('/api/data1'); // 1초
  const result2 = await fetch('/api/data2'); // 1초
  const result3 = await fetch('/api/data3'); // 1초

  console.log('순차 처리 시간:', Date.now() - start); // 약 3초
  return [result1, result2, result3];
}

async function parallelProcessing() {
  const start = Date.now();

  const [result1, result2, result3] = await Promise.all([
    fetch('/api/data1'), // 1초
    fetch('/api/data2'), // 1초
    fetch('/api/data3')  // 1초
  ]);

  console.log('병렬 처리 시간:', Date.now() - start); // 약 1초
  return [result1, result2, result3];
}
```

### 2. 디바운싱과 쓰로틀링

```javascript
// 디바운싱
function debounce(func, delay) {
  let timeoutId;

  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

// 쓰로틀링
function throttle(func, delay) {
  let lastCall = 0;

  return function (...args) {
    const now = Date.now();

    if (now - lastCall >= delay) {
      lastCall = now;
      func.apply(this, args);
    }
  };
}

// 사용 예시
const debouncedSearch = debounce((query) => {
  console.log('검색 실행:', query);
}, 300);

const throttledScroll = throttle(() => {
  console.log('스크롤 이벤트 처리');
}, 100);

// 이벤트 리스너에 적용
input.addEventListener('input', (e) => {
  debouncedSearch(e.target.value);
});

window.addEventListener('scroll', throttledScroll);
```

### 핵심 포인트:
- **동기**: 순차적 실행, 블로킹 발생
- **비동기**: 비순차적 실행, 블로킹 없음
- **콜백 → Promise → async/await** 순으로 발전
- **에러 처리**와 **메모리 관리**에 주의
- **성능 최적화**를 위한 병렬 처리 활용