---
title: "Modern C++ 알고리즘 및 STL 정리"
date: 2026-03-20 00:00:00 +0900
categories: [Algorithm]
tags: [C++, STL, Algorithm]
---

오늘 학습한 핵심 알고리즘과 현대적인 C++(C++20/23) 스타일의 STL 활용법을 정리한 문서입니다.

---

## 1. 정렬 알고리즘 (Sorting)

### **$O(n \log n)$ 정렬 (General Purpose)**
- **종류:** Merge Sort, Quick Sort, Heap Sort 등.
- **특징:** 데이터의 양이 많을 때 가장 보편적으로 사용하며, 대부분의 상황에서 효율적입니다.
- **STL 활용:**
  ```cpp
  // 기존 방식
  std::sort(v.begin(), v.end());
  // 현대적 방식 (C++20 Ranges)
  std::ranges::sort(v);
  ```

### **카운팅 정렬 (Counting Sort, $O(n+k)$)**
- **특징:** 숫자를 직접 저장하지 않고, **값의 빈도수(count)**를 배열에 기록합니다.
- **장점:** 숫자의 범위($k$)가 작을 때 압도적으로 빠르며, 메모리 효율이 좋습니다.
- **사용 예시 (10989번):** 메모리 제한이 매우 작고(8MB), 숫자의 범위가 좁을 때(1~10,000) 필수입니다.
  ```cpp
  std::array<int, 10001> counts{}; // 0으로 초기화
  while (n--) {
      int val; std::cin >> val;
      counts[val]++;
  }
  ```

---

## 2. 이진 탐색 (Binary Search)

- **시간 복잡도:** $O(\log n)$
- **전제 조건:** 데이터가 반드시 **정렬**되어 있어야 합니다.
- **원리:** 탐색 범위를 절반씩 줄여나가며 값을 찾는 방식입니다.

---

## 3. 현대적 C++ STL 도구 (C++20/23)

현대적인 C++는 `std::ranges`를 통해 컨테이너 전체를 직접 다룰 수 있어 코드가 더 간결해졌습니다.

| 함수 | 기능 | 반환값 |
| :--- | :--- | :--- |
| **`std::ranges::sort(v)`** | 데이터를 오름차순 정렬 | (없음) |
| **`std::ranges::binary_search(v, val)`** | 값이 존재하는지 확인 | `bool` (true/false) |
| **`std::ranges::lower_bound(v, val)`** | `val` **이상**인 첫 번째 위치 | 반복자(iterator) |
| **`std::ranges::upper_bound(v, val)`** | `val` **초과**인 첫 번째 위치 | 반복자(iterator) |

### **활용 예시**

```cpp
#include <algorithm>
#include <vector>

std::vector<int> v = {1, 2, 2, 2, 3, 5};
std::ranges::sort(v); // 정렬

// 1. 단순 존재 여부 확인 (10815번 스타일)
bool exists = std::ranges::binary_search(v, 2); // true

// 2. 특정 값의 개수 구하기
auto lb = std::ranges::lower_bound(v, 2); // 첫 번째 '2'의 위치
auto ub = std::ranges::upper_bound(v, 2); // '3'의 위치 (2를 초과하는 첫 번째 값)
int count = std::distance(lb, ub); // 결과: 3
```

---

## 4. 성능 및 스타일 팁

1.  **I/O 최적화:** 대량의 데이터를 다룰 때는 반드시 아래 설정을 사용합니다.
    ```cpp
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    ```
2.  **메모리 관리:** 수동으로 `new/delete`를 사용하는 대신, `std::vector`나 `std::array`를 사용하는 것이 현대적인 정석(RAII)입니다.
3.  **개행 처리:** `std::endl`은 출력 버퍼를 강제로 비우므로(flush) 성능을 저하시킵니다. 대량 출력 시에는 `'\n'`을 사용하는 것이 훨씬 빠릅니다.
4.  **C++23 출력:** 지원 환경이라면 `std::println`을 사용하는 것이 가장 현대적이고 빠릅니다. (GCC 14+, Clang 18+)
