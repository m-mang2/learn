# attributes
c++11에 추가된 문법으로 컴파일러 지시문
[[attributes명]]
* [[noreturn]]               c++11
* [[carries_dependency]]     c++11
* [[deprecated]]             c++14
* [[deprecated("reason")]]   c++14
* [[fallthrough]]            c++14
* [[nodiscard]]              c++17
* [[nodiscard("reason")]]    c++20
* [[maybe_unused]]           c++17
* [[likely]]                 c++20
* [[unlikely]]               c++20
* [[no_unique_address]]      c++20
---
### nodiscard
반환값이 버려지지 않고 사용되도록 사용
```
[[nodiscard("반환 값 버려지는 중")]] int* allocate(int size)
{
  return new int[size];
}

allocate(10); // 반환된 값을 사용하지 않기 때문에 경고 메세지, 반환 값을 꼭 사용해야할 때 
```
반환 값에 붙여도 되지만 아래와 같이 정의 앞에 붙일 경우 errorCode를 반환하는 모든곳에서 확인하도록 경고
```
enum class [[nodiscard]] errorCode {ok, error};
```

### likely, unlikely
제어문 등을 만들때 특 정 조건이 발생할 확률이 더 높다는 것을 알려주는 줘서 최적화에 도움 주는것
```
bool foo(int i )
{
  if(i > 0) [[likely]] // 대다수의 경우 0보다 큰 값이 올 경
    return ture;
  else
    return false;
}
```


