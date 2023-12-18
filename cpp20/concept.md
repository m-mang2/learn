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
gcd2(1.0f, 1.0f) // T가 실수이기 때문에 템플릿으로 변환 하지 않고 일반 gcd2(double a, double b )함수로 변경
```
## Requires clauses
* 템플릿 인자가 가져야하는 **제약조건을 표기** 하는 문법
* 컴파일 시간에 결정되는 bool 형식의 상수 값
* 반드시 bool 타입이어야 함 ** 변환은 허용되지 않음(int 안됨)**
* 컴파일 시간 함수를 호출하거나, !등을 사용할 때는 괄호가 필요
* 조건을 만족하는 템플릿이 2개 이상이 있는 경우는 안됨

## Requires clauses 함수 오버로딩
#### 특정 조건에 따른 함수 오버로딩
* 실수와 정수에 따른 오버로딩 (std::is_floating_point_v<T>, std::is_integral_v<T>)
* 포인터인 경우와 포인터가 아닌 경우에 따른 오버로딩 (std::is_pointer_v<T>)
* 반복자의 종류에 따른 오버로딩 (std::random_access_iterator<T>, std::input_iterator<T>)
* 가상함수 존재에 따른 오버로딩 (std::is_polymorphic_v<T>)

```
template<typename T> requires std::is_polymorphic_v<T> //가상함수가 존재할 때
void func(const T& arg) {}
template<typename T> requires (!std::is_polymorphic_v<T>) //가상함수가 존재하지 않을 때
void func(const T& arg) {}
```



