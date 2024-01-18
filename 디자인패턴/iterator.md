> 복합 객체 요소들의 **내부 표현 방식을 공개하지 않고도 순차적으로 접근** 할 수 있는 방법을 제공

![image](https://github.com/m-mang2/learn/assets/135841268/18248031-ab79-498f-be65-79884569ec13)

## interface 기반
java, c# swift

#### 모든 반복자는 사용법이 동일해야함
* 반복자의 인터페이스 필요
```
template<typename T> struct IEnumerator
{
	virtual T& getObject() = 0;
	virtual bool moveNext() = 0;

	virtual ~IEnumerator() {}
};
```
  
#### 모든 컨테이너에서는 반복자를 꺼낼 수 있어야함
* 반복자를 가진 컨테이너도 인터페이스 필요
```
template<typename T> struct IEnumerable  
{
	virtual IEnumerator<T>* getEnumerator() = 0;

	virtual ~IEnumerable() {}
};

```

#### 반복자 구현의 핵심 원리
* 1번째 요소를 가리키는 포인터를 보관하다가 약속된 방식으로 다음으로 이동하고 요소에 접근

```
template<typename T> 
class slist_enumerator : public IEnumerator<T>
{
	Node<T>* current;
public:
	slist_enumerator(Node<T>* p = nullptr)
		: current(p)
	{
	}

	T& getObject() override { return current->data; }

	bool moveNext() override
	{
		current = current->next;
		return current != nullptr; 
	}
};

//slist_enumerator p1(list);
//p1.moveNext();
//int n = p1.getObject();
```

#### 사용법
```
// 리스트에 IEnumerable를 상속하여 getEnumerator()를 구현
template<typename T> class slist : public  IEnumerable<T> 
{
	Node<T>* head = nullptr;
public:
	void push_front(const T& a) { head = new Node<T>(a, head); }

	IEnumerator<T>* getEnumerator() override
	{
		return new slist_enumerator<T>(head); //moveNext를 통해서 다음인자로 넘어감
	}
};

	slist<int> s;
	s.push_front(10);
	s.push_front(20);
	s.push_front(30);
	s.push_front(40);
	s.push_front(50);

	IEnumerator<int>* p = s.getEnumerator();

	std::cout << p->getObject() << std::endl; // 50
	p->moveNext();
	std::cout << p->getObject() << std::endl; // 40
```

### 단점
* new slist_enumerator<T>(head); 할당을 하였기 떄문에 직접 해제를 안하면 누수
* getObject, moveNext가 가상함수기 때문에 자주 호출된다면 오버헤드가 있음
* 배열 접근할때도 주소를 보관하고 ++ 으로 이동하는데 배열은 그럴필요가 없는데도 동일하게 작동하게됨


## c++ stl 라이브러리가 사용하는 설계 방식
* 가상 함수가 아닌 인라인 치환
* moveNext() 대신 ++연산자 재정의
* getObject() 대신 *연산자 재정의
* 반복자를 꺼내기 위해 begin(), end() 함수 제공
* 기본적인 개념은 동일하지만 c++ 테크닉

```

template<typename T> struct Node
{
	T     data;
	Node* next;
	Node(const T& d, Node* n) : data(d), next(n) {}
};

template<typename T> 
class slist_iterator
{
	Node<T>* current;
public:
	slist_iterator(Node<T>* p = nullptr)
		: current(p)
	{
	}

	inline T& operator*() { return current->data; }

	inline slist_iterator& operator++() 
	{
		current = current->next;
		return *this; 
	}

	// 이건 편의상..
	inline bool operator==(const slist_iterator& other) const 
	{
		return current == other.current;
	}
	inline bool operator!=(const slist_iterator& other) const 
	{
		return current != other.current;
	}	
};



template<typename T> class slist 
{
	Node<T>* head = nullptr;
public:
	void push_front(const T& a) { head = new Node<T>(a, head); }

	inline slist_iterator<T> begin()
	{
		return slist_iterator<T>(head);
	}
	inline  slist_iterator<T> end()
	{
		return slist_iterator<T>(nullptr);
	}
};

```
