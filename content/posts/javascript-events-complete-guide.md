+++
title = "JavaScript 이벤트 완벽 가이드 - 이벤트 핸들링 마스터하기"
date = 2025-07-14T21:16:09+09:00
draft = false
description = "JavaScript 이벤트의 모든 것! 이벤트 타입, 핸들러 등록, 전파, 위임, preventDefault, stopPropagation까지 완벽하게 정리합니다."
tags = ["javascript", "이벤트", "이벤트핸들러", "이벤트전파", "이벤트위임", "프론트엔드"]
categories = ["개발"]
+++

# JavaScript 이벤트 완벽 가이드 - 이벤트 핸들링 마스터하기

안녕하세요! 오늘은 웹 개발의 핵심인 **JavaScript 이벤트**에 대해 자세히 알아보겠습니다.

사용자의 클릭, 키보드 입력, 마우스 움직임 등 모든 상호작용은 이벤트를 통해 처리됩니다. 이 포스팅에서는 이벤트의 기본 개념부터 고급 활용까지 체계적으로 정리해보겠습니다!

## 이벤트란?

### 기본 개념
**이벤트(Event)**는 웹 페이지에서 발생하는 모든 상호작용을 의미합니다. 사용자의 마우스 클릭, 키보드 입력, 페이지 로드 등이 모두 이벤트입니다.

### 이벤트의 구성 요소
- **이벤트 타입**: 어떤 종류의 이벤트인지 (click, keydown, load 등)
- **이벤트 타겟**: 이벤트가 발생한 요소
- **이벤트 핸들러**: 이벤트 발생 시 실행될 함수

## 주요 이벤트 타입들

### 1. 마우스 이벤트

| 이벤트 타입 | 설명 | 특징 |
|-------------|------|------|
| `click` | 마우스 클릭 | 가장 일반적인 클릭 이벤트 |
| `dblclick` | 더블 클릭 | 빠르게 두 번 클릭 |
| `mousedown` | 마우스 버튼 누름 | 버튼을 누르는 순간 |
| `mouseup` | 마우스 버튼 해제 | 버튼을 떼는 순간 |
| `mousemove` | 마우스 이동 | 커서가 움직일 때 |
| `mouseenter` | 요소 진입 | 요소 안으로 들어올 때 (버블링 없음) |
| `mouseover` | 요소 진입 | 요소 안으로 들어올 때 (버블링 있음) |
| `mouseleave` | 요소 이탈 | 요소 밖으로 나갈 때 (버블링 없음) |
| `mouseout` | 요소 이탈 | 요소 밖으로 나갈 때 (버블링 있음) |

### 2. 키보드 이벤트

| 이벤트 타입 | 설명 | 특징 |
|-------------|------|------|
| `keydown` | 키 누름 | 키를 누르는 순간 |
| `keyup` | 키 해제 | 키를 떼는 순간 |
| `keypress` | 키 입력 | 문자 키 입력 시 (폐지됨) |

### 3. 폼 이벤트

| 이벤트 타입 | 설명 |
|-------------|------|
| `submit` | 폼 제출 |
| `reset` | 폼 초기화 |
| `input` | 입력값 변경 |
| `change` | 값 변경 완료 |
| `focus` | 포커스 획득 |
| `blur` | 포커스 상실 |

### 4. 문서/윈도우 이벤트

| 이벤트 타입 | 설명 |
|-------------|------|
| `load` | 페이지 로드 완료 |
| `DOMContentLoaded` | DOM 로드 완료 |
| `resize` | 윈도우 크기 변경 |
| `scroll` | 스크롤 |
| `beforeunload` | 페이지 이탈 전 |

## 이벤트 핸들러 등록 방법

### 1. 인라인 이벤트 핸들러 (비권장)

```html
<button onclick="handleClick()">클릭하세요</button>
<script>
function handleClick() {
  console.log('버튼이 클릭되었습니다!');
}
</script>
```

**단점:**
- HTML과 JavaScript가 섞여 있어 유지보수가 어려움
- 전역 스코프 오염
- 이벤트 제거가 어려움

### 2. 이벤트 핸들러 프로퍼티

```html
<button id="myButton">클릭하세요</button>
<script>
const button = document.getElementById('myButton');

button.onclick = function() {
  console.log('버튼이 클릭되었습니다!');
};

// 이벤트 제거
button.onclick = null;
</script>
```

**장점:** 간단하고 직관적
**단점:** 하나의 이벤트에 하나의 핸들러만 등록 가능

### 3. addEventListener (권장)

```html
<button id="myButton">클릭하세요</button>
<script>
const button = document.getElementById('myButton');

// 이벤트 핸들러 등록
button.addEventListener('click', function(event) {
  console.log('버튼이 클릭되었습니다!');
  console.log('이벤트 객체:', event);
});

// 여러 핸들러 등록 가능
button.addEventListener('click', function() {
  console.log('두 번째 핸들러!');
});

// 이벤트 제거
button.removeEventListener('click', handler);
</script>
```

**장점:**
- 여러 핸들러 등록 가능
- 이벤트 제거 용이
- 캡처링/버블링 단계 제어 가능

## 이벤트 전파 (Event Propagation)

### 이벤트 전파의 3단계

1. **캡처링 단계**: 상위 요소에서 하위 요소로 전파
2. **타겟 단계**: 이벤트 타겟에 도달
3. **버블링 단계**: 하위 요소에서 상위 요소로 전파

```html
<div id="outer">
  <div id="middle">
    <button id="inner">클릭하세요</button>
  </div>
</div>

<script>
const outer = document.getElementById('outer');
const middle = document.getElementById('middle');
const inner = document.getElementById('inner');

// 버블링 단계 (기본값)
outer.addEventListener('click', () => console.log('Outer - 버블링'));
middle.addEventListener('click', () => console.log('Middle - 버블링'));
inner.addEventListener('click', () => console.log('Inner - 버블링'));

// 캡처링 단계
outer.addEventListener('click', () => console.log('Outer - 캡처링'), true);
middle.addEventListener('click', () => console.log('Middle - 캡처링'), true);
inner.addEventListener('click', () => console.log('Inner - 캡처링'), true);
</script>
```

**실행 결과:**
```
Outer - 캡처링
Middle - 캡처링
Inner - 캡처링
Inner - 버블링
Middle - 버블링
Outer - 버블링
```

## 이벤트 위임 (Event Delegation)

### 이벤트 위임이란?
동적으로 생성되는 요소들에 대해 개별적으로 이벤트를 등록하는 대신, 상위 요소에 이벤트를 등록하여 하위 요소의 이벤트를 처리하는 기법입니다.

### 기본 예제

```html
<div id="container">
  <button class="btn">버튼 1</button>
  <button class="btn">버튼 2</button>
  <button class="btn">버튼 3</button>
</div>

<script>
// ❌ 비효율적인 방법 (각 버튼마다 이벤트 등록)
const buttons = document.querySelectorAll('.btn');
buttons.forEach(button => {
  button.addEventListener('click', handleClick);
});

// ✅ 이벤트 위임 (상위 요소에 이벤트 등록)
const container = document.getElementById('container');
container.addEventListener('click', function(event) {
  if (event.target.classList.contains('btn')) {
    handleClick(event);
  }
});

function handleClick(event) {
  console.log('클릭된 버튼:', event.target.textContent);
}
</script>
```

### 동적 요소 처리

```html
<div id="todoList">
  <button id="addTodo">할 일 추가</button>
</div>

<script>
const todoList = document.getElementById('todoList');
const addButton = document.getElementById('addTodo');

// 할 일 추가
addButton.addEventListener('click', () => {
  const todoItem = document.createElement('div');
  todoItem.className = 'todo-item';
  todoItem.innerHTML = `
    <span>새로운 할 일</span>
    <button class="delete-btn">삭제</button>
  `;
  todoList.appendChild(todoItem);
});

// 이벤트 위임으로 삭제 버튼 처리
todoList.addEventListener('click', function(event) {
  if (event.target.classList.contains('delete-btn')) {
    event.target.parentElement.remove();
  }
});
</script>
```

## 이벤트 객체의 주요 메서드

### 1. preventDefault()

기본 동작을 중단시킵니다.

```html
<a href="https://www.google.com" id="link">구글로 이동</a>
<form id="form">
  <input type="text" required>
  <button type="submit">제출</button>
</form>

<script>
// 링크 클릭 시 기본 동작 중단
document.getElementById('link').addEventListener('click', function(event) {
  event.preventDefault();
  console.log('링크 클릭이 차단되었습니다.');
});

// 폼 제출 시 기본 동작 중단
document.getElementById('form').addEventListener('submit', function(event) {
  event.preventDefault();
  console.log('폼 제출이 차단되었습니다.');
});
</script>
```

### 2. stopPropagation()

이벤트 전파를 중단시킵니다.

```html
<div id="outer" style="padding: 20px; background: lightblue;">
  <div id="inner" style="padding: 20px; background: lightcoral;">
    <button id="button">클릭하세요</button>
  </div>
</div>

<script>
const outer = document.getElementById('outer');
const inner = document.getElementById('inner');
const button = document.getElementById('button');

outer.addEventListener('click', () => console.log('Outer 클릭'));
inner.addEventListener('click', () => console.log('Inner 클릭'));
button.addEventListener('click', (event) => {
  console.log('Button 클릭');
  event.stopPropagation(); // 이벤트 전파 중단
});
</script>
```

### 3. stopImmediatePropagation()

이벤트 전파와 같은 요소의 다른 핸들러 실행을 모두 중단시킵니다.

```javascript
const button = document.getElementById('button');

button.addEventListener('click', function(event) {
  console.log('첫 번째 핸들러');
  event.stopImmediatePropagation(); // 다른 핸들러 실행 중단
});

button.addEventListener('click', function() {
  console.log('이 핸들러는 실행되지 않습니다');
});
```

## 실전 활용 사례

### 1. 드래그 앤 드롭 구현

```javascript
class DragAndDrop {
  constructor(element) {
    this.element = element;
    this.isDragging = false;
    this.offset = { x: 0, y: 0 };

    this.setupEventListeners();
  }

  setupEventListeners() {
    this.element.addEventListener('mousedown', this.handleMouseDown.bind(this));
    document.addEventListener('mousemove', this.handleMouseMove.bind(this));
    document.addEventListener('mouseup', this.handleMouseUp.bind(this));
  }

  handleMouseDown(event) {
    this.isDragging = true;
    this.offset.x = event.clientX - this.element.offsetLeft;
    this.offset.y = event.clientY - this.element.offsetTop;

    this.element.style.cursor = 'grabbing';
  }

  handleMouseMove(event) {
    if (!this.isDragging) return;

    this.element.style.left = (event.clientX - this.offset.x) + 'px';
    this.element.style.top = (event.clientY - this.offset.y) + 'px';
  }

  handleMouseUp() {
    this.isDragging = false;
    this.element.style.cursor = 'grab';
  }
}

// 사용 예시
const draggableElement = document.getElementById('draggable');
new DragAndDrop(draggableElement);
```

### 2. 키보드 단축키 구현

```javascript
class KeyboardShortcuts {
  constructor() {
    this.shortcuts = new Map();
    this.setupEventListeners();
  }

  register(key, callback, ctrl = false, shift = false) {
    const keyCombo = this.createKeyCombo(key, ctrl, shift);
    this.shortcuts.set(keyCombo, callback);
  }

  createKeyCombo(key, ctrl, shift) {
    const parts = [];
    if (ctrl) parts.push('Ctrl');
    if (shift) parts.push('Shift');
    parts.push(key.toUpperCase());
    return parts.join('+');
  }

  setupEventListeners() {
    document.addEventListener('keydown', (event) => {
      const keyCombo = this.createKeyCombo(
        event.key,
        event.ctrlKey,
        event.shiftKey
      );

      const callback = this.shortcuts.get(keyCombo);
      if (callback) {
        event.preventDefault();
        callback(event);
      }
    });
  }
}

// 사용 예시
const shortcuts = new KeyboardShortcuts();

shortcuts.register('s', () => {
  console.log('저장 단축키 실행');
}, true); // Ctrl+S

shortcuts.register('z', () => {
  console.log('실행 취소');
}, true); // Ctrl+Z

shortcuts.register('f', () => {
  console.log('찾기 실행');
}, true); // Ctrl+F
```

### 3. 무한 스크롤 구현

```javascript
class InfiniteScroll {
  constructor(container, loadMoreCallback) {
    this.container = container;
    this.loadMoreCallback = loadMoreCallback;
    this.isLoading = false;

    this.setupEventListeners();
  }

  setupEventListeners() {
    // 스크롤 이벤트에 쓰로틀 적용
    let ticking = false;

    this.container.addEventListener('scroll', () => {
      if (!ticking) {
        requestAnimationFrame(() => {
          this.handleScroll();
          ticking = false;
        });
        ticking = true;
      }
    });
  }

  handleScroll() {
    const { scrollTop, scrollHeight, clientHeight } = this.container;

    // 스크롤이 하단에 가까워지면 더 많은 콘텐츠 로드
    if (scrollTop + clientHeight >= scrollHeight - 100 && !this.isLoading) {
      this.loadMore();
    }
  }

  async loadMore() {
    this.isLoading = true;

    try {
      await this.loadMoreCallback();
    } catch (error) {
      console.error('콘텐츠 로드 실패:', error);
    } finally {
      this.isLoading = false;
    }
  }
}

// 사용 예시
const scrollContainer = document.getElementById('scroll-container');
const infiniteScroll = new InfiniteScroll(scrollContainer, async () => {
  // 더 많은 데이터 로드 로직
  console.log('더 많은 콘텐츠를 로드합니다...');
});
```

### 4. 폼 유효성 검사

```javascript
class FormValidator {
  constructor(form) {
    this.form = form;
    this.errors = new Map();
    this.setupEventListeners();
  }

  setupEventListeners() {
    // 실시간 유효성 검사
    this.form.addEventListener('input', (event) => {
      this.validateField(event.target);
    });

    // 폼 제출 시 전체 검사
    this.form.addEventListener('submit', (event) => {
      if (!this.validateForm()) {
        event.preventDefault();
        this.showErrors();
      }
    });
  }

  validateField(field) {
    const value = field.value.trim();
    const rules = field.dataset.rules?.split(',') || [];

    for (const rule of rules) {
      const [ruleName, ruleValue] = rule.split(':');

      if (!this.checkRule(value, ruleName, ruleValue)) {
        this.showFieldError(field, ruleName);
        return false;
      }
    }

    this.clearFieldError(field);
    return true;
  }

  checkRule(value, rule, ruleValue) {
    switch (rule) {
      case 'required':
        return value.length > 0;
      case 'min':
        return value.length >= parseInt(ruleValue);
      case 'max':
        return value.length <= parseInt(ruleValue);
      case 'email':
        return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);
      default:
        return true;
    }
  }

  validateForm() {
    const fields = this.form.querySelectorAll('[data-rules]');
    let isValid = true;

    fields.forEach(field => {
      if (!this.validateField(field)) {
        isValid = false;
      }
    });

    return isValid;
  }

  showFieldError(field, rule) {
    const errorMessage = this.getErrorMessage(rule);
    field.classList.add('error');

    let errorElement = field.parentNode.querySelector('.error-message');
    if (!errorElement) {
      errorElement = document.createElement('div');
      errorElement.className = 'error-message';
      field.parentNode.appendChild(errorElement);
    }

    errorElement.textContent = errorMessage;
  }

  clearFieldError(field) {
    field.classList.remove('error');
    const errorElement = field.parentNode.querySelector('.error-message');
    if (errorElement) {
      errorElement.remove();
    }
  }

  getErrorMessage(rule) {
    const messages = {
      required: '이 필드는 필수입니다.',
      min: '최소 길이를 만족하지 않습니다.',
      max: '최대 길이를 초과했습니다.',
      email: '유효한 이메일 주소를 입력하세요.'
    };

    return messages[rule] || '유효하지 않은 값입니다.';
  }
}

// 사용 예시
const form = document.getElementById('myForm');
new FormValidator(form);
```

## 성능 최적화 팁

### 1. 이벤트 위임 활용

```javascript
// ❌ 비효율적
document.querySelectorAll('.item').forEach(item => {
  item.addEventListener('click', handleClick);
});

// ✅ 효율적
document.addEventListener('click', (event) => {
  if (event.target.matches('.item')) {
    handleClick(event);
  }
});
```

### 2. 쓰로틀링과 디바운싱

```javascript
// 스크롤 이벤트 최적화
function throttle(func, delay) {
  let waiting = false;
  return function (...args) {
    if (!waiting) {
      func.apply(this, args);
      waiting = true;
      setTimeout(() => waiting = false, delay);
    }
  };
}

window.addEventListener('scroll', throttle(() => {
  console.log('스크롤 이벤트 처리');
}, 100));
```

### 3. 이벤트 리스너 정리

```javascript
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
```