# c++20 에서 추가된 람다 표현식의 특징
* 람다 표현식에서 **템플릿 사용가능**
* **평가 되지 않은 표현식**에서 람다 표현식 사용가능
* 캡쳐 하지 않은 람다 표현식에서 **디폴트 생성자와 대입 연산자 사용** 가능
* **암시적인 this 캡쳐가 deprecated** 됨
* **Parameter pack 캡쳐** 가능

------------------------------
## 람다 표현식에서 템플릿 사용가능
c++ 14에서의 람다

````
auto add2 = [](auto a, auto b) {return a + b; }
add2(1.1, 1) // 2.1 출력 인자가 달라도 출력
````
![image](https://github.com/mantaek/learn/assets/135841268/89f12336-13f5-4262-aa18-fa412fc88cf9)

c++20 에서의 람다
````
auto add2 = []<typename T>(T a, T b) {return a + b; }
add2(1.1, 1) // a와 b의 인자가 double와 int 이기 때문에 에러
````
---------------------------------
## 평가 되지 않은 표현식에서 람다 표현식 사용가능
실행 시간에는 실행되지 않고, 컴파일 시간에만사용 되는 표현식
* sizeof(exp) // 표현식 결과의 크기
* decltype(exp) // 표현식 결과의 타입
* typeid(exp) // 표현식 결과의 타입 정보
* nocept(exp) // 표현식 예외 여부


c++ 17까지는 객체를 사용 해야함
```
struct Freer
{
    inline void operator()(void *p) const noexcept
    {
        free(p)
    }
}
std::unique_ptr<int, Freer> up( static_cast<int*>(malloc(100)); // 2번째인자는 메모리 해제 방식

```
c++20에서는 람다 사용 가능
```
std::unique_ptr<int, decltype([](int* p){free(p);})> up( static_cast<int*>(malloc(100));
```

## 캡쳐 하지 않은 람다 표현식에서 디폴트 생성자와 대입 연산자 사용 가능
![image](https://github.com/mantaek/learn/assets/135841268/2e82bcb0-568f-43e4-99e7-3bf6ae76944d)
```
int v1 = 10;
auto f1 = [v1](int a, int b) {return a + b;}

decltype(f1) f2;         // c++17 error ,  c++20 error
decltype(f1) f3 = f1;    // c++17 ok ,  c++20 ok
f3 = f1;                 // c++17 error ,  c++20 error

auto f1 = [](int a, int b) {return a + b;}

decltype(f1) f2;          // c++17 error ,  c++20 ok
decltype(f1) f3 = f1;     // c++17 ok ,  c++20 ok
f3 = f1;                  // c++17 error ,  c++20 ok
```
## 암시적인 this 캡쳐가 deprecated 됨
맵버 함수에서의 [=]캡쳐 사용시 암묵적으로 this가 같이 캡쳐됨
만약 메모리가 해제왼 this의 맴버 변수를 사용 할 경우 문제가 생김
c++20에서 부터는 경고 표시 해줌
```
auto f1 = [=] (int a) {return a + value;} value가 this의 맴버 변수 [=]는 [=,this] 와 같음
auto f2 = [=, this] (int a) {return a + value;} 위에 암시적으로 되는 this를 명시적으로 적음, 잘 못된 메모리 참조는 여전함
auto f3 = [=, *this] (int a) {return a + value;} this를 값으로 복사
```

## Parameter pack 캡쳐 가능
파라메터 캡쳐 가능
```
// Capure Parameter Pack by value
template<typename ... Args> auto f1(Args&&... args)
{
    return [...args = std::forward<Args>(args)](){}
}

// Capure Parameter Pack by Reference
template<typename ... Args> auto f1(Args&&... args)
{
    return [&&...args = std::forward<Args>(args)](){}
}

f1(1,2,3,4,5)();
```



