+++
title = "디바운스 vs 쓰로틀 완벽 비교 가이드 - 언제 어떤 것을 사용할까?"
date = 2024-12-18T00:00:00+09:00
draft = false
description = "디바운스와 쓰로틀의 차이점을 명확히 이해하고, 실전에서 언제 어떤 것을 사용해야 하는지 결정하는 방법을 알아봅니다."
tags = ["javascript", "디바운스", "쓰로틀", "이벤트", "최적화", "프론트엔드"]
categories = ["개발"]
+++

# 디바운스 vs 쓰로틀 완벽 비교 가이드 - 언제 어떤 것을 사용할까?

이번에는 **디바운스(Debounce)**와 **쓰로틀(Throttle)**의 차이점을 명확히 이해하고, 실전에서 언제 어떤 것을 사용해야 하는지 알아보겠습니다.

## 핵심 차이점

| 특징 | 디바운스 | 쓰로틀 |
|------|----------|--------|
| **실행 시점** | 마지막 이벤트 후 일정 시간 지난 후 | 일정 시간마다 한 번씩 |
| **실행 횟수** | 그룹의 마지막에 1번만 | 주기적으로 여러 번 |
| **적합한 상황** | 입력 완료 후 실행 | 실시간 반응 필요 |

## 시각적 비교

### 디바운스 (Debounce)
```
이벤트: |--|--|--|--|--|--|--|--|--|--|
실행:                                    |
```
- 연속된 이벤트를 그룹화하여 마지막에 한 번만 실행

### 쓰로틀 (Throttle)
```
이벤트: |--|--|--|--|--|--|--|--|--|--|
실행:    |     |     |     |     |     |
```
- 일정 주기마다 한 번씩 실행

## 언제 어떤 것을 사용할까?

### 디바운스 사용 시기
- **검색창 자동완성**: 사용자가 타이핑을 멈춘 후 검색
- **폼 자동 저장**: 입력 완료 후 저장
- **윈도우 리사이즈**: 크기 조정 완료 후 레이아웃 재계산

### 쓰로틀 사용 시기
- **스크롤 이벤트**: 무한 스크롤, 고정 헤더
- **게임 캐릭터 이동**: 마우스/키보드 입력 처리
- **실시간 차트 업데이트**: 주기적인 데이터 갱신

## 실전 예제: 검색 vs 스크롤

```javascript
// 디바운스: 검색창 (입력 완료 후 실행)
const searchInput = document.getElementById('search');
searchInput.addEventListener('input', debounce((e) => {
  fetchSearchResults(e.target.value);
}, 300));

// 쓰로틀: 스크롤 (주기적 실행)
window.addEventListener('scroll', throttle(() => {
  updateHeaderPosition();
}, 100));
```

## 고급 활용: React Hook

```javascript
// 커스텀 디바운스 Hook
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

// 커스텀 쓰로틀 Hook
function useThrottle(callback, delay) {
  const lastRun = useRef(Date.now());

  return useCallback((...args) => {
    if (Date.now() - lastRun.current >= delay) {
      callback(...args);
      lastRun.current = Date.now();
    }
  }, [callback, delay]);
}

// 사용 예시
function SearchComponent() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    if (debouncedQuery) {
      fetchSearchResults(debouncedQuery);
    }
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="검색어를 입력하세요"
    />
  );
}
```

## 성능 최적화 팁

### 1. 적절한 딜레이 설정
```javascript
// 검색: 300ms (사용자가 타이핑을 멈출 시간)
const searchDebounce = debounce(searchFunction, 300);

// 스크롤: 100ms (부드러운 반응)
const scrollThrottle = throttle(scrollFunction, 100);

// 리사이즈: 500ms (레이아웃 재계산 비용 고려)
const resizeDebounce = debounce(resizeFunction, 500);
```

### 2. 메모리 누수 방지
```javascript
// 컴포넌트 언마운트 시 정리
useEffect(() => {
  const debouncedHandler = debounce(handleInput, 300);

  input.addEventListener('input', debouncedHandler);

  return () => {
    input.removeEventListener('input', debouncedHandler);
  };
}, []);
```

### 3. 조건부 적용
```javascript
// 네트워크 상태에 따른 동적 조정
function adaptiveDebounce(callback, baseDelay = 300) {
  const isSlowConnection = navigator.connection?.effectiveType === 'slow-2g';
  const delay = isSlowConnection ? baseDelay * 2 : baseDelay;

  return debounce(callback, delay);
}
```

## 실제 프로젝트 적용 사례

### 1. 쇼핑몰 검색 기능
```javascript
class SearchManager {
  constructor() {
    this.searchInput = document.getElementById('search');
    this.resultsContainer = document.getElementById('results');
    this.setupEventListeners();
  }

  setupEventListeners() {
    // 디바운스: 검색 요청 최적화
    this.searchInput.addEventListener('input', debounce((e) => {
      this.performSearch(e.target.value);
    }, 300));

    // 쓰로틀: 검색 결과 스크롤
    this.resultsContainer.addEventListener('scroll', throttle(() => {
      this.handleInfiniteScroll();
    }, 200));
  }

  async performSearch(query) {
    if (query.length < 2) return;

    try {
      const results = await fetch(`/api/search?q=${query}`);
      this.displayResults(await results.json());
    } catch (error) {
      console.error('검색 실패:', error);
    }
  }

  handleInfiniteScroll() {
    const { scrollTop, scrollHeight, clientHeight } = this.resultsContainer;

    if (scrollTop + clientHeight >= scrollHeight - 100) {
      this.loadMoreResults();
    }
  }
}
```

### 2. 대시보드 실시간 업데이트
```javascript
class DashboardManager {
  constructor() {
    this.charts = document.querySelectorAll('.chart');
    this.setupRealTimeUpdates();
  }

  setupRealTimeUpdates() {
    // 쓰로틀: 차트 업데이트 (실시간성 유지)
    window.addEventListener('resize', throttle(() => {
      this.resizeCharts();
    }, 100));

    // 디바운스: 설정 저장 (입력 완료 후)
    this.setupAutoSave();
  }

  setupAutoSave() {
    const settingsForm = document.getElementById('settings');

    settingsForm.addEventListener('change', debounce(() => {
      this.saveSettings();
    }, 1000));
  }

  async saveSettings() {
    const formData = new FormData(settingsForm);

    try {
      await fetch('/api/settings', {
        method: 'POST',
        body: formData
      });
      console.log('설정 저장 완료');
    } catch (error) {
      console.error('설정 저장 실패:', error);
    }
  }
}
```

## 디버깅 팁

### 1. 실행 횟수 모니터링
```javascript
function createMonitoredDebounce(callback, delay) {
  let callCount = 0;

  const debouncedFunction = debounce((...args) => {
    callCount++;
    console.log(`디바운스 실행 횟수: ${callCount}`);
    callback(...args);
  }, delay);

  return debouncedFunction;
}

function createMonitoredThrottle(callback, delay) {
  let callCount = 0;

  const throttledFunction = throttle((...args) => {
    callCount++;
    console.log(`쓰로틀 실행 횟수: ${callCount}`);
    callback(...args);
  }, delay);

  return throttledFunction;
}
```

### 2. 성능 측정
```javascript
function measurePerformance(func, name) {
  return function (...args) {
    const start = performance.now();
    const result = func.apply(this, args);
    const end = performance.now();

    console.log(`${name} 실행 시간: ${end - start}ms`);
    return result;
  };
}

// 사용 예시
const optimizedSearch = measurePerformance(
  debounce(searchFunction, 300),
  '디바운스 검색'
);
```

### 핵심 포인트:
- **디바운스**: 입력 완료 후 실행 (검색, 저장)
- **쓰로틀**: 주기적 실행 (스크롤, 실시간 업데이트)
- **성능 최적화**: 적절한 딜레이 설정과 메모리 관리
- **실전 적용**: 프로젝트 특성에 맞는 패턴 선택
