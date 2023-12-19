기본 모양
```
// concept 생성
template<typename T>
concept Integral = std::is_integral_v<T>

// 만들어진 concept 사용
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

#### 추가 예제
```
template<typename T>
concept LessThancomparable = requires(T a, T b)
{
  a < b
}

template<typename T>
concept LessThancomparable2 = requires(T a, T b) 
{
   {a < b} -> std::convertible_to<bool>; // 결과값이 bool로 변환이 되어야한다
}

template<typename T>
concept Equality = requires(T a, T b)
{
    {a == b} -> std::convertible_to<bool>; // ==, != 연산이 가능해야함
    {a != b} -> std::convertible_to<bool>;
}

template<typename T>
concept Container = requires(T c)
{
    c.begin(); // 컨테이너는 begin과 end를 호출 할수 있어야함
    c.end();
}

template<typename T>
concept HasValueType = requires(T c)
{
    typename T::value_type; // value_type가 있어야함, stl에 컨테이너에 들어 있음
}
```
**물론 concepts 헤더에 여러 종류가 있어서 가져다 쓰면 됨**

#### 2개의 조건을 만족할 경우에는 모호하기 때문에 컴파일 에러가 남
```
template<typename T>
concept concept1 = sizeof(T) > 1;

template<typename T>
concept concept2 = sizeof(T) > 1 && sizeof(T) < 8;

template<typename T> requires concept1<T>
void func(T a){}

template<typename T> requires concept2<T>
void func(T a){}

func(3); // sizeof = 4 
```
#### 2개의 조건을 할 경우에는 하나의 조건에 + a를 더 하면됨(바로 제약 조건을 쓰지말고 기존거를 활용해야함)
```
template<typename T>
concept concept1 = sizeof(T) > 1;

template<typename T>
concept concept2 = concept1 && sizeof(T) < 8;
```

## 여러가지 표기법(컴파일러마다 지원이 안될수 있으니 되는거 쓰자..)
```
template<typename T> requires std::integral<T>
void f1(T a)
{}

template<typename T>
void f1(T a) requires std::integral<T> //  template옆에 아닌 함수옆에 써도됨
{}

template<std::integral T> // typename 대신 써도 가능
void f1(T a)
{}

void f1(std::integral auto a) // template을 지움
{}

void f1(auto a) // template<typename T> void f1( T a) 와 같음 그렇지만 컴파일러 지원이 안될 수 있음..
{}
```

## 표준 Concept
![image](https://github.com/m-mang2/learn/assets/135841268/187ef314-5042-4282-a1ba-38d11f38e87e)



