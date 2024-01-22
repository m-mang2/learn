> 클래스의 인스턴스는 오직 하나임을 보장하며 어디서든 동일한 방법으로 접근하는 방법을 제공

## 단점
* 전역 변수
* 멀티스레드 문제
* 객체간 결합도 증가

## 규칙
* 외부에서 객체를 생성 할수 없어야함
  * private 생성자
* 한개의 객체는 만들수 있어야함
  * 오직 한개의 객체를 반환하는 static 멤버 함수
* 복사 생성자도 사용할 수 없어야함
  * 복사/대입 금지 (=delete)

## 만드는 방법
1. meyer's singleton
   * 지연된 초기화
   * 멀티 스레드에 안전
       * static 지역 변수는 멀티스레드에 안전
```
class Cursor
{
private:
	Cursor() 
	{
		std::cout << "start  Cursor()" << std::endl;
		std::this_thread::sleep_for(3s);
		std::cout << "finish Cursor()" << std::endl;		
	}
	Cursor(const Cursor& ) = delete;
	Cursor& operator=(const Cursor&) = delete;

public:
	static Cursor& get_instance()
	{
		std::cout << "start  get_instance" << std::endl;
		static Cursor instance;
		std::cout << "finish get_instance" << std::endl;
		return instance;
	}
};
```

   
