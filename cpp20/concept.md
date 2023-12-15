타입이 가져야하는 요구 조건을 정의하는 문법

```
template<typename T>
concept GreaterThan4 = sizeof(T) >= 4;

// requires 사용
template<typename T> requires GreaterThan4<T>
void foo(T arg) {}

int i = 10;
foo(i) // 사용가능

short s = 10;
foo(s) // error 2바이트라서 사용불
```
## requires
템플릿 인자가 가져야 하는 **제약 조건을 표기**하는 문법 -> 조건 만족했을 때만 템플릿 사용

장점
* 읽기 좋은 에러 메시지 출력
* **치환 실패는 에러가 아니다.**라는 SFINAE규칙 적용 ( SFINAE : 치환 실패는 에러가 아님)
* 타입이 아닌 **조건에 따른 함수 오버로딩**
* 동일 이름의 다른 버전의 함수가 있다면 사용 가능

## 동일 이름의 다른 버전의 함수가 있다면 사용 가능
```
double gcd2(double a, double b ){return 0;}
template<typename T>
T gcd2(T a, T b) { return  a % b; }
gcd2(1.1, 1.1) // gcd2(double a, double b ) 으로 변환
gcd2(1, 1) // template<typename T> T gcd2(T a, T b) { return  a % b; } 으로 변환
gcd2(1.0f, 1.0f) // template<typename T> T gcd2(T a, T b) { return  a % b; } 으로 변환해서 a % b에서 에러 실수는 %할수 없음
```
```
double gcd2(double a, double b ){return 0;}
template<typename T> requires std::is_integral_v<T>
T gcd2(T a, T b) { return  a % b; }

gcd2(1.1, 1.1) // gcd2(double a, double b ) 으로 변환
gcd2(1, 1) // T가 정수이기 때문에 template<typename T> requires std::is_integral_v<T> T gcd2(T a, T b) { return  a % b; } 으로 변환
gcd2(1.0f, 1.0f) // T가 실수이기 때문에 템플릿으로 변환 하지 않고 일반 gcd2(double a, double b )함수로 변

```
