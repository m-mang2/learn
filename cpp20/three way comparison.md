## 관계연산자
==, !=, <, <=, >, >=

## three way comparison (spaceship operator)
<=>
(a <=> b) < 0  -> a < b
(a <=> b) > 0  -> a > b
(a <=> b) = 0  -> a == b

```
int ret = a <=> b; // ret 의 실제 반환값은 std::strong_ordering
if(ret >0) ....
```

새로운 자료형을 만들때 관계연산자(==, !=, <, <=, >, >=)를 모두 만들어야하나 <=>로 만들면 편함
```
class Int32
{
    int value;
    explicit Int32(int v = 0) : value(v) {}

    // Int32 형과 비교
    auto operator<=>(const Int32& other) const { return value <=> ohter.value; }
    bool operator==(const Int32& other) const { return value == ohter.value; }
    // auto operator<=>(const Int32& other) const = default; 이렇게하면 자동으로 <=>, == 를 자동으로 생성해주며 맴버가 놓인 순서대로 비교해줌(단 비교가 안되는 변수가 있으면 실패)

    // int와 비교
    auto operator<=>(int other) const { return value <=> ohter; }
    bool operator==(int other) const { return value == ohter; }

}
Int32 n1(10); Int32 n2(20);

아래의 경우가 다 가능
n1 == n2 // n1.operator==(n2)
n1 != n2 // !(n1 == n2)
n1 < n2 // (n1<=>n2) < 0
n1 > n2 // (n1<=>n2) > 0
n1 < 20 // (n1<=>10) < 0
20 < n1 // (10<=>n1) < 0 : error -> (n1<=>10) > 0로 변경
```
**== 연산자를 따로 만드는 이유는 비교 연산자의 경우 최적화를 여러방식으로 할 수 있기 때문에**

## Rewrite Expresstion
(10<=>n1) < 0 : error -> (n1<=>10) > 0로 변경 되는것처럼 **컴파일 시간에 다시 작성**하는 개념
![image](https://github.com/m-mang2/learn/assets/135841268/1af11788-43b1-431a-a5ba-fb6df7fac504)

## three way comparison (spaceship operator)의 반환값
* std::strong_ordering // 같다는 의미 3 * 4 == 3 * 4
* std::wak_ordering // 대등하다는 의미 1 * 12 == 3 * 4 // 값은 같지만 a, b 변수는 다름
* std::partial_ordering
 
![image](https://github.com/m-mang2/learn/assets/135841268/360c4adf-ff4a-4fda-ac88-aa8f4ea54fb2)

#### std::strong_ordering, std::wak_ordering
```
std::weak_ordering operator<=>(const Rect& r )const
{
    return w*h <=> (r.w * r.h); 
}
auto ret = r1 <=> r2;
if(ret == std::std::strong_ordering::equal) {} // 이런식으로 완전 같은것인지 비슷한것인지 확인도 가
if(ret == std::std::wak_ordering::equal) {}
```

![image](https://github.com/m-mang2/learn/assets/135841268/5b8c3cbf-b5c0-46dc-b63b-5a8ce93a3f52)

#### std::partial_ordering
std::partial_ordering::unordering // 비교가 불가능할 때

![image](https://github.com/m-mang2/learn/assets/135841268/95f01fb6-8c6b-4f6f-8eb2-35afdca6eb4e)
