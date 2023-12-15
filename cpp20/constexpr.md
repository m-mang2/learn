* std::is_constant_evaluated() 추가
* constexpr virtual function
* constexpr algorithm
* constexpr std::string& std::vector
* consteval
* constinit

## is_constant_evaluated
주어진 함수가 **컴파일 시간에 수행되는 지를 조사**하는 함수
```
constexpr auto func()
{
  return std::is_constant_evaluated();
}
```

## constexpr virtual function
```
constexpr virtual int foo(); // 20 부터 지원
```

## constexpr algorithm
현재 vc에서 안되는중...
```
constexpr int x[10] = {1,2,3,4,5,6,7,8,9,10};
constexpr int c = std::accumulate(std::begin(x), std::end(x), 0); // x도 constexpr 이어야함
constexpr auto d = std::find(std::begin(x), std::end(x), 3); // x도 constexpr 이어야함
```

## consteval

### constexpr function
* 함수가 **컴파일 시간 또는 실행시간에 수행** 될 수 있음
```
constexpr int add(int a, int b) {return a+b;}
int x = 1, y =2;
int ret = add(x, y); // 실행 시간 수행
int ret = add(1, 2); // 컴파일 시간 수행
```

### consteval
*  함수는 반드시 컴파일 시간에만 수행되어야 
```
consteval int add(int a, int b) {return a+b;}
int x = 1, y =2;
int ret = add(x, y); // error
int ret = add(1, 2); // 컴파일 시간 수행
```

## constinit
초기 값을 컴파일 시간에 알아야 하며 constexptr과는 다르게 상수가 아니기 때문에 값 변경 가능
전역 변수 선언시 순서가 보장 안되는데 constinit를 사용하면 **컴파일 타임에 초기화** 보장


