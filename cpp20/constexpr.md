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
