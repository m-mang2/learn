타입이 가진 요구조건들을 가진 이름을 가진 집합
```
// concept 생성
template<typename T>
concept Integral = std::is_integral_v<T>

// 만들어진 concept 사
template<typename T> requires Integral<T>
```
장점
* 여러개의 제약 조건을 하나의 이름으로 표현
* 제약 조건을 표현하는 **requires expresstion 문법** 제공
* 조건을 만족하는 2개 이상의 템플릿이 있을때 **ambiguous 에러에 대한 해결 방법**


### requires expresstion
만들어진 std::is_integral_v<T> 대신에 실제로 만드는것
```
// requires expresstion와 requires clauses는 다름
// requires의 위치를 잘 확
template<typename T>
concept Moulus = requires(T a, T b) // requires expresstion
{
  a % b
}

template<typename T> Moulus<T> // requires clauses

```
