* explicit(bool)
* ranged-for with initializer
* using enum

## explicit(bool)
explicit
* 복사 생성자 사용 할 수 없음
* 생성자를 사용한 암시적 변환을 사용 할수 없음

explicit(bool)
* 클래스 설계시 **특정 조건에 따라 생성자를 explicit로 지정**하는 문법
* 클래스 템플릿에 주로 사용
```
template<typename T> class Wrapper
{
    T data;
public:
    //is_intergral_v 정수형인지 확
    explicit (std::is_intergral_v<T>) Wrapper(T value) : data(Wrapper) {}
}

Wrapper w1("a"); // 문자형이라 explicit(false)가 되어 적용 안함
Wrapper w2(10); // 정수라서 explicit(true)가 되어 적용
```

## ranged-for with initializer
c++ 11
* 새로운 반복문인 **ranged-for** 도입
```
for(auto n : x){}
```

c++ 17
* 초기화 구문을 가진 제어문 도임
```
if( int ret = foo(); ret == 0 ){}
switch( int ret = foo(); ret ){}
```
  

c++ 20
* **초기화 구문을 가진 ranged-for** 도입
```
for(int x[3] {1,2,3}; auto n : x) {}
```

## using enum

```
enum class color{red}

switch(color)
{
    using enum color; // color::red로 해야하나 color 생
    case red:...
}
```

