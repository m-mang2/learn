개념
* 아이템들의 집합
* begin(), end()를 사용할 수 있어야 함

```
std::vector<int> v = {1,2,3,4,5,6,7,8,9,10};
auto r1 = v |  std::views::take(3); // 3개 까지만 가져옴, 1, 2, 3
위 코드는 아래랑 같음
std::ranges::take_view tv(v, 3);
```

## Views
![image](https://github.com/m-mang2/learn/assets/135841268/977fbd36-d1f6-4131-8a3b-8a42f2e24306)
* 지연된 연산을 함
```
std::vector<int> v = {1,2,3,4,5,6,7,8,9,10};
std::ranges::transform_view tv(v, [](int a) { return a * 2; });

auto p1 = std::ranges::begin(tv)
v[0] = 10;
std::cout << *p1 << std::endl; //2가 아닌 20이 나옴 지금 호출할때 연산이 됨
```
* 연산식 조합이 가능함
```
std::vector<int> v = {1,2,3,4,5,6,7,8,9,10};
std::ranges::take_view tv(v, 3);
std::ranges::transform_view trv(tv, [](int a) { return a * 2; });
이거 대신에 "|" 으로 한번에 가능

auto r = v | std::views::take(3) | std::views::transform([](int a) {return a*2;});
```
* 중첩이 가능함
```
std::vector<int> v = {1,2,3,4,5,6,7,8,9,10};
std::ranges::filter_view fv1(v, [](int a) { return a * 2 == 0;});
std::ranges::take_view tv(fv1, 3);
std::ranges::reverse_view trv(tv);

f1, tv, trv 전부 6, 4, 2가 나옴

auto r = v | std::views::filter_view([](int a) { return a * 2 == 0;}) | std::views::take(3) | std::views::reverse_view(); // fv1, tv 같은 변수 생략 가능

```
* 
## 뷰 종류
![image](https://github.com/m-mang2/learn/assets/135841268/e4da126d-6f5b-4471-8cce-14b2475454c7)

## 기존 stl알고리즘과 ranges 알고리즘
1. 기존의 알고리즘 인자로 **반복자** 전달
2. RAnge알고리즘은 인자로 **Range** 전

