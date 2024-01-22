>요소에 대한 연산을 정의하는 개체를 만들어서 객체의 모든 요소에 연산을 수행하는 패턴

>클래스를 변경하지 않고 새로운 오퍼레이션을 정의할 때

어떻게  모든 요소를 도는지 만드는게 핵심

규칙
* s.accept()는 다양한 종류의 방문자를 개체로 받을수 있어야한다 (IVisitor)
* 방문의 대상(list, vector)은 accept()가있어야한다 (IAcceptor)

![image](https://github.com/m-mang2/learn/assets/135841268/44800ce2-f7ae-4671-b377-8a107a1066f5)
![image](https://github.com/m-mang2/learn/assets/135841268/25807723-7526-4ab3-8edb-139bf404a94f)

```
template<typename T> struct IVisitor
{
	virtual void visit(T& e) = 0;
	virtual ~IVisitor() {}
};

template<typename T> 
class TwiceVisitor : public IVisitor<T>
{
public:
	void visit(T& e) override { e *= 2; }
};

template<typename T> 
class ShowVisitor : public IVisitor<T>
{
public:
	void visit(T& e) override
	{
		std::cout << e << ", ";
	}
};

template<typename T> struct IAcceptor
{
	virtual void accept(IVisitor<T>* visitor) = 0;
	virtual ~IAcceptor() {}
};

template<typename T> 
class MyList : public std::list<T>, public IAcceptor<T>
{
public:
	using std::list<T>::list;

  // 여기가 핵심..
  // 결국 모든걸 순회하면서 일관적인 작업을 함
	void accept(IVisitor<T>* visitor) override
	{
		for( auto& e : *this)
			visitor->visit(e);
	}
};

int main()
{
	MyList<int> s = { 1,2,3,4,5,6,7,8,9,10 };

	TwiceVisitor<int> tv; 
	s.accept(&tv);

	ShowVisitor<int> sv; 
	s.accept(&sv);	
}
```

객체 지향 디자인의 특징
* 새로운 요소를 추가하는 것은 쉬움
* 새로운 가상함수를 추가하는것은 어려움
![image](https://github.com/m-mang2/learn/assets/135841268/9d4363f4-c317-4460-9192-b7823816f068)

방문자 패턴을 사용하면
* 새로운 요소를 추가하는 것은 어려움* 
* 새로운 가함함수를  추가하는것이 쉬워짐

결국 객체지행 디자인의 특징을 반대로 사용할일이 많다면 방문재 패턴을 고려해 볼것
(새로운 가함함수가 많아짐)
