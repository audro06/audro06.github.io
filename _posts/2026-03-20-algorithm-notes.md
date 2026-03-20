---
title: "Modern C++ 알고리즘 및 STL 정리"
date: 2026-03-20 00:00:00 +0900
categories: [Algorithm]
tags: [C++, STL, Algorithm]
---

ryute님의 티스토리에 있는 boj길라잡이를 바탕으로 공부를 시작했다.

---

## 1. 정렬 알고리즘 (Sorting)

### **$O(n \log n)$ 정렬 (General Purpose)**
  ```cpp
  #include <algorithm>
  // 기존 방식
  std::sort(v.begin(), v.end());
  // 현대적 방식 (C++20 Ranges)
  std::ranges::sort(v);
  ```
- ranges::를 사용해 벡터나 정적 배열 자체를 인자로 넘길 수 있다.
  ```cpp
  #include <algorithm>
  #include <span>

  std::sort(arr, arr+n); // n은 배열의 길이
  std::sort(std::begin(arr), str::end(arr));
  //c++20부터 아래처럼 가능하다.
  std::ranges::sort(arr);
  //다만, 동적 배열의 경우 컴파일러가 크기를 모르기에
  int size = 5;
  int* ptr = new int[size]{5,2,1,8,9};
  //size를 직접 넘겨야 한다.
  std::sort(ptr, ptr + size);
  //ranges를 쓸 수도 있긴 하다.
  std::ranges::sort(ptr, ptr + size);
  //span을 써서 한번에 보내거나.(굳이?)
  std::ranges::sort(std::span{ptr, size});
  delete [] ptr; //해제 잊지 말기.
  ```
- gemini가 이르시길, 굳이 span을 쓰는 이유는 함수에 넘겨줄 때 한번에 넘겨주기에 편하기 때문이라고 한다.  또 참조만 하기도 하며, 하나의 함수에 span으로 받으면 vector나 array나 동적 배열이나 한번에 처리하게 할 수 있단다.
```cpp
  void printAndSort(std::span<int> data) {
    std::ranges::sort(data);
    // 출력 로직...
}

int main() {
    std::vector<int> vec = {3, 1, 2};
    std::array<int, 3> arr = {6, 4, 5};
    int* ptr = new int[3]{9, 7, 8};

    // 모두 똑같은 함수에 던져넣을 수 있음!
    printAndSort(vec);
    printAndSort(arr);
    printAndSort(std::span{ptr, 3}); // 포인터만 이렇게 묶어서 전달
```
- 추가로 파이프라인 연산을 지원한다고 하는데..
```cpp
  #include <iostream>
  #include <span>
  #include <ranges>

  int main() {
    int* ptr = new int[5]{5, 2, 8, 1, 9};

    // 동적 할당된 배열에서 짝수만 걸러내서 출력하고 싶을 때
    auto evens = std::span{ptr, 5} 
               | std::views::filter([](int n) { return n % 2 == 0; });

    for (int n : evens) {
        std::cout << n << " "; // 2, 8 출력
    }
  }
```
### **카운팅 정렬 (Counting Sort, $O(n+k)$)**
- **특징:** 숫자를 직접 저장하지 않고, **값의 빈도수(count)**를 배열에 기록
- **장점:** 숫자의 범위($k$)가 작을 때 압도적으로 빠르며, 메모리 효율이 좋음
- **사용 예시 (10989번):** 메모리 제한이 매우 작고(8MB), 숫자의 범위가 좁을 때
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
- **전제 조건:** 데이터 **정렬** 필요
- **원리:** 탐색 범위를 절반씩 줄여나가며 값을 찾음
---

## 3. STL (C++20/23)

현대적인 C++는 `std::ranges`를 통해 컨테이너 전체를 직접 다룸
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

// 1. 단순 존재 여부 확인 (10815번)
bool exists = std::ranges::binary_search(v, 2); // true

// 2. 특정 값의 개수 구하기
auto lb = std::ranges::lower_bound(v, 2); // 첫 번째 '2'의 위치
auto ub = std::ranges::upper_bound(v, 2); // '3'의 위치 (2를 초과하는 첫 번째 값)
int count = std::distance(lb, ub); // 결과: 3
```

---

## 4. 성능 및 스타일 팁

1.  **Fast IO:** 입출력 시간초과를 당하지 않기 위해
```cpp
    //cin, cout을 printf와 같은 c의 입출력과 연결을 끊어서 속도를 확보한다. 다만, 해당 설정을 적용하면, printf 등을 섞어 사용하지 못한다.
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    //false나 nullptr 타이핑이 귀찮으면 그냥 0을 써도 된다.
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);

    ```
2.  **메모리 관리:** 수동으로 `new/delete`를 사용하는 대신, `std::vector`나 `std::array`를 사용하는 것이 현대적인 방식이라고 하니, 이를 쓰도록 노력하자.
