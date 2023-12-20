![image](https://github.com/m-mang2/learn/assets/135841268/a391552b-4703-40f2-a3f6-857b48a573b7)

#### Generator의 규칙
1. **promise_type**이 있어야 함
2. std::coroutine_handle<> 맴버
3. 인자가 한 개인 생성자
4. 소멸자에서 coroutine 파괴

#### Promise의 규칙
1. get_return_object
2. initial_suspend
3. return_void
4. final_suspend
5. unhandled_exception

```
class Generator
{
public:
    sturct Promise
    {
        Generator get_return_object() // Promise의 규칙 1
        {
            return Generator { std:: coroutine_handle<Promise>::from_promise(*this); }
        }
        auto initial_suspend() {return std::suspend_always{}; } // Promise의 규칙 2
        auto return_void() { return std::suspend_never{}; } // Promise의 규칙 3
        auto final_suspend() { return std::suspend_always{}; } // Promise의 규칙 4
        void unhandled_exception() {std::exit(1);} // Promise의 규칙 5
    }
    using promise_type = Promise;  // Generator의 규칙 1 promise_type이 있어야 함
    std::coroutune_handle<promise_type> coro; //  Generator의 규칙 2  std::coroutine_handle<> 맴버
    Generator(coroutune_handle<promise_type> c) : coro(c) {} // Generator의 규칙 3 인자가 한 개인 생성자
    ~Generator() {if ( coro ) coro.destroy(); } // Generator의 규칙 4 소멸자에서 coroutine 파괴


};
```

```
Generator foo()
{
    co_await std::suspend_always{};  // 콜한곳으로 돌아가는 구문
}

```
이 구문을 만들면 컴파일러에서 이렇게 변환함
그래서 Generator에 규칙으로 만들어진 코드들이 필요함
```
Generator foo()
{
    Generator::promise_type pro;
    Generator g = pro.get_return_object();
    co_await pro.initial_suspend();
    try
    {
        co_await std::suspend_always{}; 
    }
    catch(...)
    {
        pro.unhandled_exception();
    }
    pro.return_void()
    co_await pro.final_suspend();
}

int main()
{
    Generator g = foo();
    g.coro.resume(); // 첫번째 resume

    g.coro.resume(); // 두번째 resume
}
```

![image](https://github.com/m-mang2/learn/assets/135841268/d4c301bd-bf89-4972-a33d-b3248f053cb2)
#### 만들어진 구문으로 해설(?)
1. promise타입의 객체가 만들어짐
2. get_return_object() 함수가 호출 되고 여기서는 Generator타입에 자기 자신(promise)타입을 넣어서 반환함
3. main 함수와 foo()를 왔다 갔다 하기 위해서는 힙에 메모리를 저장하고 있어야 함
4. Generator객체 안에는 coro가 있는데 이건 힙을 가리키고 부가적인 데이터를 더 가지고있음
5. initial_suspend()함수는 결국 std::suspend_always{}를 호출 하는것인데 이건 본래의 함수로 돌아가는 함수 그래서 initial_suspend가 호출되자마자 main으로감
6. g.coro.resume()를 호출하면 다시 foo()으로 돌아가고 try안의 std::suspend_always{}를 만나서 다시 돌아옴
7. 다시 main으로 와서 두번째 resume을 만나면 코루틴으로 돌아가고 final_suspend를 만나면 종료

