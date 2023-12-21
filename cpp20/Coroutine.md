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


## 값을 보내고 싶을때
```
Generator foo()
{
    co_yield 10;  // co_await pro.yield_value(10); 으로 컴파일러가 변경하기 때문에 promise에 yield_value를 추가해야함
}

class Generator
{
public:
    sturct Promise
    {
        int value; // 값을 저장할 맴버변수 추가
        Generator get_return_object()
        {
            return Generator { std:: coroutine_handle<Promise>::from_promise(*this); }
        }
        auto yield_value(int n) // 컴파일러에 맞춘 함수 추가 
        {
            value = n
            return std::suspend_always{};
        }
        auto initial_suspend() {return std::suspend_always{}; }
        auto return_void() { return std::suspend_never{}; }
        auto final_suspend() { return std::suspend_always{}; }
        void unhandled_exception() {std::exit(1);}
    }
    using promise_type = Promise;
    std::coroutune_handle<promise_type> coro;
    Generator(coroutune_handle<promise_type> c) : coro(c) {}
    ~Generator() {if ( coro ) coro.destroy(); }
};

main에서는 g.coro.promise().value를 호출하면 10이 있
```

## iter 추가
```
template<T>
class Generator
{
public:
    sturct Promise
    {
        T value; // 값을 저장할 맴버변수 추가
        T getValue() {return value;}
        Generator get_return_object()
        {
            return Generator { std:: coroutine_handle<Promise>::from_promise(*this); }
        }
        auto yield_value(T n) // 컴파일러에 맞춘 함수 추가 
        {
            value = n
            return std::suspend_always{};
        }
        auto initial_suspend() {return std::suspend_always{}; }
        auto return_void() { return std::suspend_never{}; }
        auto final_suspend() { return std::suspend_always{}; }
        void unhandled_exception() {std::exit(1);}
    }
    using promise_type = Promise;
    std::coroutune_handle<promise_type> coro;
    Generator(coroutune_handle<promise_type> c) : coro(c) {}
    ~Generator() {if ( coro ) coro.destroy(); }

    class Iter
    {
    public:
        void operator++(){coro.resume();}
        const T& operator*() const {return coro.promise.getValue();}
        bool operator==(std::default_sentinel_t) const {return !coro || coro.done();}
        explicit Iter(std::coroutine_handle<promise_type> coroutine) : coro{coroutine} {}
    private:
        std::coroutine_handle<promise_type> coro;
    }
    Iter begin() {if(coro) {coro.resume();} return {coro}; }
    std::default_sentinel_t end() return {}; }
};

Generator<int> Gen(int first, int last)
{
    for(int i = first ; i <= last ; i++)
        co_yield i;
}

Generator<int> g = get(10, 20);
for(auto n : g) // iter 추가시 이게 가능
    cout<< n<< endl;
```


## await Object
```
co_await std::suspend_always{};
```
```
std::suspend_always awaiter;
co_await awaiter; 
```
두개는 같은 의미의 코드

#### co_await awaiter;가 호출 될때 내부적으로 만들어지는 코드
![image](https://github.com/m-mang2/learn/assets/135841268/8adf5102-193d-4036-8935-c44e2141e0bc)
#### suspend_always와 suspend_never의 내부 함수
![image](https://github.com/m-mang2/learn/assets/135841268/28f65953-ae17-4d12-88b2-abab894eb9e9)

#### await_ready, await_resume를 새로 정의하여 다른 스레드를 사용하는 로직
```
struct resume_new_thread
{
    void await_suspend(std::corotine_handle<> handle)
    {
        std::thread t([handle]() {handle.resume();});
        t.detach();
    }
    constexpr bool await_ready() const noexcept {return false;}
    constexpr void await_resume() coonst noexcept {}
};

Generator foo()
{
    // 주 스레드
    cout << std::this_thread::get_id();
    co_await resume_new_thread{};
    // 메인함수에서 돌아왓을때 여기서 부턴 새로운 스레드에서 코루틴 함수가 진행되어 스레드 아이디가 다
    cout << std::this_thread::get_id();
}
```
1. if(!awaiter.await_ready())가 false니까 if안으로 들어감
2. await_suspend함수가 호출되어서 코루틴 핸들값으로 새로운 스레드를 만듦
3. suspend가 호출되어 코루틴을 호출한 main함수로 돌아감
4. resume으로 다시 코루틴 함수로 돌아올땐 awaiter는 새로운 스레드를 만들었기 때문에 작업을 새로운 스레드에서 진행함



